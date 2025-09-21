# stm32-rotary-encoder-uart
## 📌 Project Overview
This project demonstrates how to interface a **rotary encoder** with an **STM32 microcontroller** using the **Timer Encoder Mode**.  
The encoder pulses are counted by **TIM2** and the counter value is sent via **UART2** to a PC terminal (e.g., RealTerm or PuTTY).  

This is useful in robotics, motor control, user input devices (knobs), and measurement systems where precise angle or position feedback is required.

---

## ⚡ Features
- Read **quadrature signals (CLK & DT)** from a rotary encoder
- Use **TIM2 in Encoder Mode** to count pulses automatically
- Configure counter range (0 → 65535)
- Print counter values over **UART (115200 baud)**
- Visualize results using **RealTerm** on PC
- Use **NodeMCU board as USB-to-Serial adapter**

---

## 🛠️ Hardware Requirements
- **STM32F103C8T6 ("Blue Pill")** or similar STM32 board  
- **Rotary Encoder (Incremental, 2-channel: DT & CLK)**  
- **NodeMCU (ESP8266) board used as USB-to-Serial adapter**  
- Jumper wires  

---

## 🔌 Connections

### 🔹 Encoder → STM32
- Encoder `CLK` → STM32 `PA0` (TIM2_CH1)  
- Encoder `DT` → STM32 `PA1` (TIM2_CH2)  
- Encoder `GND` → STM32 `GND`  
- Encoder `VCC` → STM32 `5V`

### 🔹 NodeMCU → STM32 (as Serial Adapter)
- **RST ↔ GND** (to disable ESP8266 and use only USB-to-Serial)  
- NodeMCU `RX` ↔ STM32 `RX` (PA3, USART2_RX)  
- NodeMCU `TX` ↔ STM32 `TX` (PA2, USART2_TX)  
- NodeMCU `GND` ↔ STM32 `GND`  
  

---

## 🔧 Software Requirements
- **STM32CubeIDE** (or Keil uVision)  
- **STM32 HAL Library**  
- **RealTerm** (or PuTTY / TeraTerm) for serial monitor  

---

## 📜 Code Explanation
### 1. Timer Setup (Encoder Mode)
```c
htim2.Instance = TIM2;
htim2.Init.Prescaler = 0;
htim2.Init.CounterMode = TIM_COUNTERMODE_UP;
htim2.Init.Period = 65535;   // 16-bit max
...
sConfig.EncoderMode = TIM_ENCODERMODE_TI1;
sConfig.IC1Polarity = TIM_ICPOLARITY_RISING;
sConfig.IC2Polarity = TIM_ICPOLARITY_RISING;
HAL_TIM_Encoder_Init(&htim2, &sConfig);
```
TIM2 is set to Encoder Mode
The counter increases/decreases based on the encoder’s rotation
Max value = 65535 (16-bit)

### 2. Starting the Encoder
```c
HAL_TIM_Encoder_Start(&htim2, TIM_CHANNEL_ALL);
```
This enables the timer to start counting pulses from the encoder.

### 3. Reading the Counter
```c
uint32_t counter = __HAL_TIM_GetCounter(&htim2);
```
This returns the current encoder position.
Rotating clockwise → counter increases
Rotating counter-clockwise → counter decreases

### 4. Sending Data Over UART
```c
printf("the counter register : %d\r\n", counter);
```
printf is redirected to UART2 using fputc(), so all print statements are visible in RealTerm.

---
##Applications

- Motor position & speed measurement
- CNC machines & robotics
- Volume knobs or user input dials
- Precise angular displacement measurement
