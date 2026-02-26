# A (VietSub)
### MCU có thực trắng 100% không ?

Có tồn tại, nhưng chủ yếu là MCU đời cũ hoặc MCU cực đơn giản hoặc đặc biệt
Các dòng gần như trắng: 
  - Dòng AVR cổ điển (VD: ATmega16)
  - Dòng PIC đời cũ (VD: PIC16F84)
  - 8051 Flash MCU cũ (VD: AT89S52)
  - FPGA softcore MCU

Nhưng, MCU phổ biến hiện đại (đặc biệt là ARM Cortex-M) thì không. Các MCU hiện tại đều có SystemROM trong ROM, ví dụ như:
  - AVR USB đời mới (ATmega32U4)
  - Microchip (SAM series)
  - STMicroelectronics (STM32F103, STM32F407,...)
  - Espressif (ESP32)

Điểm chung là SystemROM của MCU đều có BootROM 

### BootROM trong MCU
BootROM là mã cố định trong ROM nội, chạy ngay sau reset.
Nó là tầng thấp nhất của quá trình khởi động.
  - Khởi động hệ thống (Boot initialization)
    -  ádsad

