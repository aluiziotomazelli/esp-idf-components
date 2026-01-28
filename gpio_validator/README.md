# GPIO Validator Component

[![Build GPIO Validator Multi-Target](https://github.com/aluiziotomazelli/esp-idf-components/actions/workflows/gpio_validator.yml/badge.svg)](https://github.com/aluiziotomazelli/esp-idf-components/actions/workflows/gpio_validator.yml)

A lightweight ESP-IDF component to validate GPIO pins at runtime. It helps prevent common hardware conflicts by checking if a pin is reserved for SPI Flash, PSRAM, JTAG, or if it functions as a strapping pin for the specific chip model in use.

## Features

- **Multi-Target Support**: Specific validation rules for ESP32, ESP32-S3, and ESP32-C3.
- **Prohibited Pin Detection**: Returns errors for pins that would crash the system (e.g., Flash/PSRAM pins).
- **Cautionary Warnings**: Logs warnings for JTAG, UART0, and Boot strapping pins.
- **Input-Only Awareness**: Identifies pins that do not support output mode or lack pull-up/pull-down resistors.

## Installation

1. Copy the `gpio_validator` folder to your project's `components` directory.
2. Include the header in your code:
```cpp
#include "gpio_validator.hpp"
```

## API Reference

### Enum: GpioValidator::Mode
Defines the intended usage of the pin.
- Mode::INPUT: Validates the pin for input usage.
- Mode::OUTPUT: Validates the pin for output usage.

### Static Method: validate
```cpp
static esp_err_t validate(gpio_num_t gpio, Mode mode);
```
Performs the validation based on the detected chip at runtime.

**Parameters:**
- gpio: The GPIO number (gpio_num_t).
- mode: The intended GpioValidator::Mode.

**Returns:**
- ESP_OK: The pin is safe to use for the specified mode.
- ESP_ERR_INVALID_ARG: The pin is prohibited (Flash/PSRAM) or does not support the requested mode.

## Example Usage

```cpp
#include "gpio_validator.hpp"
#include "esp_log.h"

void setup_my_hardware() {
    gpio_num_t my_pin = GPIO_NUM_12;

    // Validate if GPIO 12 is safe for Output
    if (GpioValidator::validate(my_pin, GpioValidator::Mode::OUTPUT) == ESP_OK) {
        // Safe to initialize the peripheral
        ESP_LOGI("App", "GPIO %d is safe to use", my_pin);
    } else {
        ESP_LOGE("App", "GPIO %d is NOT safe or invalid for Output", my_pin);
    }
}
```

## Logging
The component uses the standard ESP_LOG system:
- Errors (Red): For prohibited pins that will cause system failure.
- Warnings (Yellow): For pins that might interfere with debugging (JTAG) or boot sequences (Strapping pins).