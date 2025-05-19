# Snake Game on EasyMx PRO M4 (ARM)

A simple implementation of the classic “Snake” game for the EasyMx PRO M4 development board.  
The game uses a TFT ILI9341 display (240×320) and a 4-direction joystick. A special “wall-phasing” bonus lets the snake pass through walls for a short time.

---

## Features

- **Joystick Control**  
  Move the snake up, down, left, right via a 4-button joystick.
- **Collision Detection**  
  - Snake vs. walls  
  - Snake vs. its own body
- **Food Mechanics**  
  - Randomly spawning food  
  - Snake grows by 1 unit each time it eats
- **“Wall-Phasing” Bonus**  
  - Appears periodically on the board  
  - Grants temporary ability to pass through walls
- **Scoring System**  
  - Current score displayed on screen  
  - Tracks and displays **High Score**
- **Game Over Screen**  
  - “Game Over” message on collision  
  - Option to restart immediately

---

## Hardware & Software Requirements

| Component                  | Details                                  |
| :------------------------- | :--------------------------------------- |
| **Development Board**      | EasyMx PRO M4 (ARM Cortex-M series)      |
| **Display**                | TFT ILI9341, 240×320 pixels              |
| **Input**                  | 4-direction joystick                     |
| **Toolchain**              | ARM GCC (or other Cortex-M compatible)   |
| **Libraries**              | • TivaWare (peripherals)  
• GrLib (graphics)  
• ILI9341 driver  

---

## Wiring

1. **TFT Display (ILI9341)**  
   - **MOSI** → SPI MOSI  
   - **SCK**  → SPI SCK  
   - **CS**, **DC**, **RST** → GPIO  
2. **Joystick**  
   - Up/Down/Left/Right → GPIO inputs with pull-ups  

_(Refer to your board’s silkscreen or schematics for exact pin numbers.)_

---

## Build & Flash

```bash
git clone https://github.com/SperaKrul1/Snake_ARM.git

