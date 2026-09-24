# Automatic Sensor-Based LED Control System Using STM32

## Aim

To interface a digital sensor with an **STM32 microcontroller** and automatically control an LED according to the sensor output.

---

## Apparatus Required

| S. No. | Component | Quantity |
|---:|---|---:|
| 1 | STM32 Nucleo-L031K6 development board | 1 |
| 2 | Digital sensor / Push Button | 1 |
| 3 | LED | 1 |
| 4 | 220–330 Ω Resistor | 1 |
| 5 | Breadboard | 1 |
| 6 | Jumper Wires | As required |
| 7 | USB Cable | 1 |
| 8 | Wokwi Simulator | 1 |

---

## Theory

A sensor-based LED control system is used to automatically control an LED according to the output of a sensor.

In this experiment, a **digital sensor or push button** is used as the sensor input. The sensor output is connected to **PA0** of the STM32 Nucleo-L031K6.

The sensor provides either a **HIGH (logic 1)** or **LOW (logic 0)** signal.

The STM32 reads the digital signal at PA0 and controls the LED connected to **PA5**.

The operation is:

- When the sensor output is **HIGH**, the LED is turned **ON**.
- When the sensor output is **LOW**, the LED is turned **OFF**.

Thus, the STM32 automatically controls the LED according to the digital sensor input.

---

## Pin Configuration

| Component | STM32 Pin | Function |
|---|---|---|
| Digital Sensor Output | PA0 | Digital Input |
| LED Anode | PA5 through 220–330 Ω resistor | Digital Output |
| LED Cathode | GND | Ground |
| Sensor VCC | 3.3V | Power Supply |
| Sensor GND | GND | Ground |

---

## Circuit Connections

### Digital Sensor / Push Button

| Sensor Pin | STM32 Connection |
|---|---|
| VCC | 3.3V |
| GND | GND |
| OUT / SIG | PA0 |

### LED

| LED Pin | STM32 Connection |
|---|---|
| Anode (+) | PA5 through 220–330 Ω resistor |
| Cathode (−) | GND |

---

## Circuit Diagram

~~~text
             Digital Sensor
          +------------------+
   3.3V --| VCC              |
   GND  --| GND              |
          | OUT              |
          +-------+----------+
                  |
                  |
                 PA0
                  |
                  v
        +-----------------------+
        | STM32 Nucleo-L031K6   |
        |                       |
        | PA0 -> Digital Input  |
        |                       |
        | PA5 -> Digital Output |
        +-----------+-----------+
                    |
                    |
                  220Ω
                    |
                    |
                   LED
                    |
                   GND
~~~

---

## Block Diagram

~~~text
      Digital Sensor
            |
            | Digital Signal
            v
       +----------+
       |   PA0    |
       |   ADC    |
       |  Input   |
       +----+-----+
            |
            v
+-------------------------+
| STM32 Nucleo-L031K6     |
|                         |
| Read Sensor Input       |
|          |              |
|          v              |
| Control LED             |
+------------+------------+
             |
             | PA5
             v
           LED
             |
             v
            GND
~~~

---

## Algorithm

1. Start the program.
2. Initialize the STM32 HAL library.
3. Configure the system clock.
4. Configure **PA0 as a digital input**.
5. Configure **PA5 as a digital output**.
6. Read the digital signal from PA0.
7. Check whether the sensor output is HIGH or LOW.
8. If the sensor output is HIGH, set PA5 HIGH and turn ON the LED.
9. If the sensor output is LOW, set PA5 LOW and turn OFF the LED.
10. Repeat the process continuously.
11. Stop.

---

## Program

```c
#include "main.h"

void SystemClock_Config(void);
static void MX_GPIO_Init(void);

int main(void)
{
    GPIO_PinState sensor_state;

    /* Initialize HAL Library */
    HAL_Init();

    /* Configure System Clock */
    SystemClock_Config();

    /* Initialize GPIO */
    MX_GPIO_Init();

    while (1)
    {
        /* Read sensor input from PA0 */
        sensor_state = HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0);

        /* Check sensor condition */
        if (sensor_state == GPIO_PIN_SET)
        {
            /* Sensor HIGH - Turn ON LED */
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
        }
        else
        {
            /* Sensor LOW - Turn OFF LED */
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
        }
    }
}


/*------------------------------------------------
  GPIO Initialization
------------------------------------------------*/
static void MX_GPIO_Init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct = {0};

    /* Enable GPIOA Clock */
    __HAL_RCC_GPIOA_CLK_ENABLE();

    /* Configure PA0 as Digital Input */
    GPIO_InitStruct.Pin = GPIO_PIN_0;
    GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
    GPIO_InitStruct.Pull = GPIO_PULL_DOWN;
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

    /* Configure PA5 as Digital Output */
    GPIO_InitStruct.Pin = GPIO_PIN_5;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

    /* Initially turn OFF LED */
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
}


/*------------------------------------------------
  System Clock Configuration
------------------------------------------------*/
void SystemClock_Config(void)
{
    RCC_OscInitTypeDef RCC_OscInitStruct = {0};
    RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

    /* Configure MSI Oscillator */
    RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_MSI;
    RCC_OscInitStruct.MSIState = RCC_MSI_ON;
    RCC_OscInitStruct.MSIClockRange = RCC_MSIRANGE_6;
    RCC_OscInitStruct.MSICalibrationValue = 0;

    HAL_RCC_OscConfig(&RCC_OscInitStruct);

    /* Configure CPU, AHB and APB clocks */
    RCC_ClkInitStruct.ClockType =
        RCC_CLOCKTYPE_HCLK |
        RCC_CLOCKTYPE_SYSCLK |
        RCC_CLOCKTYPE_PCLK1;

    RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_MSI;
    RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
    RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV1;

    HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_0);
}
## Result

The digital sensor was successfully interfaced with the STM32 microcontroller. The LED connected to PA5 turned ON when the sensor input at PA0 was HIGH and turned OFF when the sensor input was LOW.
