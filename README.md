# TX2 – Tay cầm điều khiển DIY (STM32F411) + ExpressLRS (RP2 V1.2)

Dự án **TX2** là tay cầm điều khiển DIY sử dụng **STM32F411** để điều khiển UAV/quad thông qua liên kết vô tuyến **ExpressLRS (ELRS)**.  
Tay cầm đọc tín hiệu từ joystick/switch, chuẩn hoá thành các kênh điều khiển và **gửi khung CRSF qua UART** vào **RadioMaster RP2 V1.2** (đã cấu hình để hoạt động như TX module công suất thấp). Trên drone sử dụng **RP2 V1.2 làm Receiver** và **Flight Controller (FC) Betaflight** để nhận kênh điều khiển.

---

## Tính năng chính

- Đọc tín hiệu **joystick/switch/potentiometer** qua ADC + MUX (tuỳ theo phần cứng).
- Tạo dữ liệu điều khiển **16 kênh** theo chuẩn RC (roll/pitch/yaw/throttle + AUX).
- Gửi dữ liệu điều khiển dưới dạng **CRSF (Crossfire protocol)** qua **UART2** sang module ELRS.
- (Tuỳ phiên bản code) Nhận **telemetry/link statistics** từ module (LQ/RSSI/SNR) để hiển thị lên OLED/LED/buzzer.
- Cơ chế **failsafe** cơ bản khi mất tín hiệu.

---

## Kiến trúc hệ thống

### Phía tay cầm (TX)
STM32F411 (TX2)
- Đọc joystick/switch
- Mapping/mixing kênh
- Đóng gói CRSF RC Channels
- Gửi CRSF qua UART2 → RP2 V1.2 (TX module)

### Phía drone (RX)
RP2 V1.2 (Receiver)
- Nhận RF ELRS
- Xuất CRSF qua UART → Flight Controller (Betaflight)

---

## Phần cứng sử dụng

- MCU: **STM32F411**
- Module ELRS:
  - **RadioMaster RP2 V1.2** (1 cái flash làm TX module công suất thấp)
  - **RadioMaster RP2 V1.2** (1 cái làm Receiver trên drone)
- Drone: Quad + FC chạy **Betaflight**
- (Tuỳ chọn) OLED / buzzer / LED trạng thái

---

## Kết nối phần cứng

### 1) STM32F411 (UART2) ↔ RP2 (TX module)
> UART nối chéo TX/RX và chung GND.

- STM32 **PA2 (USART2_TX)** → RP2 **RX**
- STM32 **PA3 (USART2_RX)** ← RP2 **TX**
- STM32 **5V** → RP2 **5V**
- STM32 **GND** → RP2 **GND**

**Baudrate khuyến nghị:** `400000` (phù hợp cấu hình ELRS ở mức packet rate ≤ 250Hz)

---

### 2) RP2 (Receiver) ↔ Flight Controller (Betaflight)
- RP2 **TX** → FC **RX (UARTx_RX)**
- RP2 **RX** → FC **TX (UARTx_TX)** 
- RP2 **5V** → FC **5V**
- RP2 **GND** → FC **GND**

---

## Cấu hình ExpressLRS (RP2 V1.2)

### TX module (RP2 được dùng như TX)
- Flash bằng **ExpressLRS Configurator**
- Chọn target RP2 V1.2 phù hợp
- Bật chế độ: **Use receiver as a transmitter (TX module)** 
- **Binding phrase:** ví dụ `12345678`
- **Receiver baud rate:** `400000`

### Receiver (RP2 trên drone)
- Flash cùng target RP2 V1.2
- **Binding phrase** phải **GIỐNG HỆT** TX (vd `12345678`) để tự bind

---



