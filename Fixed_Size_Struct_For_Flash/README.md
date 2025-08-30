# Fixed-Size Struct for Flash Storage (Engsub)

## Overview
This code ensures a structure has a fixed size of **1024 bytes**, useful for:
- Hardware/firmware communication.
- Binary file read/write with fixed-size blocks.
- Memory mapping or binary protocols requiring strict block size

## Implementation Example

```c++
void* register_address = 0x08010000; // Example flash address

typedef struct INFORMATION_FLASH_SET
{
    uint32_t Signature;
    uint32_t Version;
    uint32_t Information[MAX_NUMBER];
} INFORMATION_FLASH_SET;

struct INFORMATION_FLASH_SET_WITH_PADDING : public INFORMATION_FLASH_SET
{
    uint8_t _padding[1024 - sizeof(INFORMATION_FLASH_SET)];
};

#define Flash_InformationSet ((const INFORMATION_FLASH_SET_WITH_PADDING*) register_address)
```

_padding[...] is a byte array used for padding to ensure that the total size of the struct is exactly 1024 bytes.
By subtracting the current struct size from 1024, you get the number of bytes that need to be added.
This way, _padding contains enough empty bytes so that when combined with the inherited part, the total struct size is 1024 bytes.

- Ensures a fixed struct size (in this case, 1024 bytes).
- This is often needed when:
    - Communicating with peripheral devices (firmware, microcontrollers).
    - Reading/writing binary files with a fixed-size structure.
    - Synchronizing with a binary protocol or memory mapping that has a fixed block size.

After that, you can easily store a value of type INFORMATION_FLASH_SET into flash memory.

```c++
static INFORMATION_FLASH_SET Flash_InformationSetInRam;
...
Flash_Write ((uint32_t) Flash_InformationSet, &Flash_InformationSetInRam, 1024, 72000);
```

# Cấu Trúc Cố Định Kích Thước cho Bộ Nhớ Flash (VietSub)
## Tổng Quan 

Đoạn code này đảm bảo một cấu trúc dữ liệu có kích thước cố định **1024 bytes**, hữu ích cho:  
- Giao tiếp giữa phần cứng và firmware.  .  
- Đọc/ghi file nhị phân với các khối dữ liệu kích thước cố định.  
- Ánh xạ bộ nhớ hoặc các giao thức nhị phân yêu cầu kích thước khối nghiêm ngặt.  

## Ví Dụ Triển Khai  

```c++
void* register_address = 0x08010000; // Ví dụ địa chỉ flash

typedef struct INFORMATION_FLASH_SET
{
    uint32_t Signature;
    uint32_t Version;
    uint32_t Information[MAX_NUMBER];
} INFORMATION_FLASH_SET;

struct INFORMATION_FLASH_SET_WITH_PADDING : public INFORMATION_FLASH_SET
{
    uint8_t _padding[1024 - sizeof(INFORMATION_FLASH_SET)];
};

#define Flash_InformationSet ((const INFORMATION_FLASH_SET_WITH_PADDING*) register_address)
```
_padding[...] là một mảng byte dùng để đệm (padding) nhằm đảm bảo tổng kích thước của struct mới đúng bằng 1024 bytes. Lấy 1024 trừ đi giá trị đó sẽ ra số byte cần bổ sung. Như vậy _padding sẽ chứa đủ byte rỗng để khi cộng với kích thước phần kế thừa, tổng struct = 1024 bytes.

- Đảm bảo kích thước struct cố định (ở đây là 1024 bytes). 
- Điều này thường cần khi: 
    - Giao tiếp với thiết bị ngoại vi (firmware, vi điều khiển). 
    - Truyền/ghi dữ liệu ra file nhị phân với cấu trúc kích thước cố định. 
    - Đồng bộ với một binary protocol hoặc memory mapping có kích thước block cố định.

Sau đó bạn có thể lưu giá trị kiểu INFORMATION_FLASH_SET vào flash 1 cách dễ dàng

```c++
static INFORMATION_FLASH_SET Flash_InformationSetInRam;
...
Flash_Write ((uint32_t) Flash_InformationSet, &Flash_InformationSetInRam, 1024, 72000);
```