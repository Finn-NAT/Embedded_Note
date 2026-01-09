# Issue note: `esp_ota_set_boot_partition()` triggers FreeRTOS assert when called from a small-stack task

Date: 2026-01-09  
Target: ESP32-S3, ESP-IDF v5.3.4  
Repo/workspace: `ota_bootloader/`

## Summary
When calling `esp_ota_set_boot_partition(factory)` from a user task created with a small stack (e.g. `xTaskCreate(..., 2048, ...)`), the device can crash with a FreeRTOS assert such as:

```
assert failed: xQueueSemaphoreTake queue.c:1713 (pxQueue->uxItemSize == 0)
```

Increasing the task stack to a larger value (e.g. `4096`) makes the crash disappear.

This points to **task stack overflow / memory corruption** caused by insufficient stack for the call chain (logging + OTA/partition/flash operations), which later manifests as an RTOS internal consistency assert.

## System setup (as used here)
- Factory firmware: `http_bootloader/main/simple_ota_example.c` (acts as factory fallback app)
- OTA firmware: `app_project_test/main/app.c`
- OTA firmware behavior: on button press, call:
  - `esp_ota_set_boot_partition(factory)`
  - then `esp_restart()`

## Symptoms
- Pressing the button in the OTA app (non-factory partition) prints:
  - `Switching boot partition to factory ...`
- Then the device crashes with an assert and backtrace.
- After crash, the chip reboots.
- Backtrace often contains frames in:
  - FreeRTOS queue/semaphore
  - UART VFS / VFS
  - partition / bootloader_support / NVS
  - and may map to `simple_ota_example.c` if monitoring with that project’s ELF.

## Crash log (captured example)

This is the log observed when pressing the button in the OTA app and switching boot partition back to `factory`:

```text
W (3158) factory_rollback: Switching boot partition to factory (label=factory offset=0x00010000) and restarting...

assert failed: xQueueSemaphoreTake queue.c:1713 (pxQueue->uxItemSize == 0)


Backtrace: 0x40375a62:0x3fc9a0d0 0x4037a575:0x3fc9a0f0 0x40381095:0x3fc9a110 0x4037abf2:0x3fc9a230 0x4037ad0e:0x3fc9a270 0x40376a05:0x3fc9a290 0x40376ae5:0x3fc9a2c0 0x42007b1b:0x3fc9a2e0 0x42008f55:0x3fc9a300 0x4200f48a:0x3fc9a320 0x42008a80:0x3fc9a340 0x42008f55:0x3fc9a360 0x4200eeca:0x3fc9a380 0x4200e705:0x3fc9a3a0 0x4200e756:0x3fc9a3c0 0x4200ed49:0x3fc9a3e0 0x42012e03:0x3fc9a410 0x420127e1:0x3fc9a430 0x4200ef41:0x3fc9a750 0x40380e09:0x3fc9a780 0x40380dd9:0x3fc9a7b0 0x4200bb26:0x3fc9a800 0x4200bbb2:0x3fc9a860 0x4200bcce:0x3fc9a8a0 0x4200bd59:0x3fc9a8d0 0x4200ab8e:0x3fc9a8f0 0x4200add7:0x3fc9aa20 0x420097b1:0x3fc9aa40 0x4037add9:0x3fc9aa70
--- 0x40375a62: esp_restart_noos at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_system/port/soc/esp32s3/system_internal.c:118
--- 0x4037a575: ram_enable_wifi_agc at ??:?
--- 0x40381095: gpspi_flash_ll_set_dummy_out at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/hal/esp32s3/include/hal/gpspi_flash_ll.h:384
--- (inlined by) spi_flash_hal_gpspi_configure_host_io_mode at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/hal/spi_flash_hal_common.inc:136
--- 0x4037abf2: esp_cpu_unstall at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_hw_support/cpu.c:41
--- 0x4037ad0e: gdma_start at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_hw_support/dma/gdma.c:533
--- 0x40376a05: vPortExitCriticalSafe at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/freertos/FreeRTOS-Kernel/portable/xtensa/include/freertos/portmacro.h:596
--- (inlined by) esp_intr_disable at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_hw_support/intr_alloc.c:912
--- 0x40376ae5: xPortEnterCriticalTimeoutSafe at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/freertos/FreeRTOS-Kernel/portable/xtensa/include/freertos/portmacro.h:579
--- (inlined by) vPortEnterCriticalSafe at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/freertos/FreeRTOS-Kernel/portable/xtensa/include/freertos/portmacro.h:588
--- (inlined by) wifi_bt_common_module_disable at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_hw_support/periph_ctrl.c:116
--- 0x42007b1b: adjust_boot_time at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/newlib/time.c:63
--- 0x42008f55: uart_access at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_driver_uart/src/uart_vfs.c:383
--- 0x4200f48a: process_segment_data at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/bootloader_support/src/esp_image_format.c:671
--- 0x42008a80: uart_tcgetattr at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_driver_uart/src/uart_vfs.c:892
--- 0x42008f55: uart_access at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_driver_uart/src/uart_vfs.c:383
--- 0x4200eeca: bootloader_common_check_chip_validity at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/bootloader_support/src/bootloader_common_loader.c:92
--- 0x4200e705: load_partitions at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_partition/partition.c:182
--- 0x4200e756: load_partitions at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_partition/partition.c:196
--- 0x4200ed49: bootloader_common_get_sha256_of_partition at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/bootloader_support/src/bootloader_common.c:164
--- 0x42012e03: nvs::Storage::writeItem(unsigned char, nvs::ItemType, char const*, void const*, unsigned int) at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/nvs_flash/src/nvs_storage.cpp:381
--- 0x420127e1: nvs::Storage::calcEntriesInNamespace(unsigned char, unsigned int&) at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/nvs_flash/src/nvs_storage.cpp:903
--- 0x4200ef41: bootloader_common_check_chip_validity at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/bootloader_support/src/bootloader_common_loader.c:108
--- 0x40380e09: gpspi_flash_ll_set_read_mode at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/hal/esp32s3/include/hal/gpspi_flash_ll.h:236
--- 0x40380dd9: gpspi_flash_ll_set_read_mode at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/hal/esp32s3/include/hal/gpspi_flash_ll.h:233
--- 0x4200bb26: http_post_status at C:/Users/Tuan/esp/v5.3.4/esp-idf/examples/my_project/ota_bootloader/http_bootloader/main/simple_ota_example.c:287
--- 0x4200bbb2: http_status_task at C:/Users/Tuan/esp/v5.3.4/esp-idf/examples/my_project/ota_bootloader/http_bootloader/main/simple_ota_example.c:312
--- 0x4200bcce: get_sha256_of_partitions at C:/Users/Tuan/esp/v5.3.4/esp-idf/examples/my_project/ota_bootloader/http_bootloader/main/simple_ota_example.c:586
--- 0x4200bd59: simple_ota_example_task at C:/Users/Tuan/esp/v5.3.4/esp-idf/examples/my_project/ota_bootloader/http_bootloader/main/simple_ota_example.c:136
--- 0x4200ab8e: get_local_fd at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/vfs/vfs.c:342
--- (inlined by) esp_vfs_fcntl_r at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/vfs/vfs.c:599
--- 0x4200add7: esp_vfs_rename at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/vfs/vfs.c:728
--- 0x420097b1: uart_get_baudrate at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_driver_uart/src/uart.c:379
--- 0x4037add9: mspi_timing_ll_set_core_clock at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/hal/esp32s3/include/hal/mspi_timing_tuning_ll.h:167
--- (inlined by) s_mspi_flash_set_core_clock at C:/Users/Tuan/esp/v5.3.4/esp-idf/components/esp_hw_support/port/esp32s3/mspi_timing_config.c:29


ELF file SHA256: 322b34af8
--- Warning: Checksum mismatch between flashed and built applications. Checksum of built application is 61fc08a828d328e8e911157de594deb728a49bf77c9cdf4dc3550fe5c63da469

Rebooting...
...
```

## Reproduction (minimal)
In the OTA app, create a task which does debouncing + logging + OTA boot partition switch:

- Task created with stack **2048** (words) → crash may happen.
- Task created with stack **4096** (words) → stable.

Example:
```c
xTaskCreate(gpio_task_example, "gpio_task_example", 2048, NULL, 10, NULL); // may crash
xTaskCreate(gpio_task_example, "gpio_task_example", 4096, NULL, 10, NULL); // stable
```

## Root cause (most likely)
### 1) Task stack too small for the call chain
The button task performs or triggers:
- `ESP_LOG*` / `printf` (newlib formatting + locks)
- partition APIs (`esp_partition_find_first`, `esp_ota_get_running_partition`)
- OTA API `esp_ota_set_boot_partition` (may validate image, read/write otadata, lock flash access)

This stack usage can exceed a small task stack size.

### 2) Why the assert looks unrelated (`xQueueSemaphoreTake`)
A stack overflow can corrupt adjacent memory. In ESP-IDF/FreeRTOS, that corruption can:
- damage queue/semaphore control blocks,
- damage pointers/handles,
- later cause asserts inside kernel code (like `xQueueSemaphoreTake`) that appear “random”.

So the assert is often a **symptom** of corruption, not the original bug.

### Note on stack units (important)
For `xTaskCreate`, ESP-IDF uses FreeRTOS semantics: the stack size parameter is in **words**, not bytes.
- On Xtensa (ESP32-S3) a word is 4 bytes.
- `2048` words ≈ 8 KB
- `4096` words ≈ 16 KB

## Fix
Increase the stack size of the task that calls OTA + heavy logging.

In this case:
- Change `xTaskCreate(..., 2048, ...)` → `xTaskCreate(..., 4096, ...)`.

Optional additional hardening (recommended if this happens again):
- Reduce logging/printf inside the button path.
- Avoid doing heavy operations inside a tight loop.
- Ensure `esp_restart()` is only called after log flush/delay.

## How to prevent / diagnose quickly next time
1) Enable stack overflow checking:
   - `menuconfig` → `Component config` → `FreeRTOS` → enable stack overflow checking.
2) Enable heap poisoning / heap debugging:
   - `menuconfig` → `Component config` → `Heap memory debugging`.
3) Print stack high water mark from the task while testing:
   - Use `uxTaskGetStackHighWaterMark(NULL)` to confirm margin.
4) Ensure you are decoding backtraces with the correct ELF:
   - “Checksum mismatch between flashed and built applications” usually means you are monitoring with an ELF that doesn’t match the running firmware.

## Expected result after fix
- Button press successfully sets boot partition to `factory` and restarts.
- No FreeRTOS assert.
- Device boots into the factory app (`simple_ota_example`).

## References
- ESP-IDF OTA APIs: `esp_ota_set_boot_partition`, `esp_ota_get_running_partition`
- FreeRTOS task stack sizing: stack specified in words
