---
title: "LED Blink with Button Control"
description: "A beginner Arduino project to control an LED with a push button"
tags:
  - arduino
  - project
  - beginner
status: new
---

# LED Blink with Button Control

A classic starter project that teaches digital input/output fundamentals.

## Overview

Learn how to read a button press and control an LED. This foundational project introduces you to digital pins, pull-up resistors, and basic circuit design.

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Beginner |
| **Time Required** | 30 minutes |
| **Category** | Arduino |
| **Last Updated** | December 2025 |

---

## What You'll Learn

- Reading digital input from a push button
- Controlling LED output with digitalWrite()
- Understanding pull-up resistors
- Basic breadboard wiring

---

## Prerequisites

- Arduino IDE installed
- Basic understanding of circuits
- Familiarity with breadboards

---

## Materials & Tools

### Hardware
| Component | Quantity | Notes |
|-----------|----------|-------|
| Arduino Uno | 1 | Or compatible board |
| LED (any color) | 1 | 5mm standard |
| Push Button | 1 | Momentary switch |
| 220Ω Resistor | 1 | For LED |
| 10kΩ Resistor | 1 | For button (optional with INPUT_PULLUP) |
| Breadboard | 1 | Half-size or larger |
| Jumper Wires | 6+ | Male-to-male |

### Software
- Arduino IDE 2.0+

---

## Circuit Diagram

```
    Arduino Uno
    ┌─────────────┐
    │         D13 ├──────┐
    │             │      │
    │          5V ├──┐   │
    │             │  │   ▼
    │         GND ├──┼───●───[220Ω]───[LED]───GND
    │             │  │
    │          D2 ├──┼───●───[Button]───GND
    │             │  │   │
    └─────────────┘  └───┴───[10kΩ]───5V (if not using INPUT_PULLUP)
```

---

## Step-by-Step Guide

### Step 1: Set Up the Circuit

1. Place the LED on the breadboard
2. Connect the longer leg (anode) through a 220Ω resistor to pin 13
3. Connect the shorter leg (cathode) to GND
4. Place the push button across the breadboard gap
5. Connect one side to pin 2, the other to GND

### Step 2: Write the Code

```cpp title="led_button_control.ino"
// LED Blink with Button Control
// Pin definitions
const int buttonPin = 2;    // Push button connected to pin 2
const int ledPin = 13;      // LED connected to pin 13

// Variables
int buttonState = 0;        // Current button state

void setup() {
  pinMode(ledPin, OUTPUT);          // Set LED pin as output
  pinMode(buttonPin, INPUT_PULLUP); // Use internal pull-up resistor
}

void loop() {
  // Read the button state (LOW when pressed due to pull-up)
  buttonState = digitalRead(buttonPin);
  
  if (buttonState == LOW) {
    digitalWrite(ledPin, HIGH);  // Turn LED on
  } else {
    digitalWrite(ledPin, LOW);   // Turn LED off
  }
}
```

### Step 3: Upload and Test

1. Connect your Arduino via USB
2. Select the correct board and port in Arduino IDE
3. Click **Upload**
4. Press the button - the LED should light up!

---

## Enhancements to Try

???+ tip "Toggle Mode"
    Modify the code to toggle the LED state with each button press:
    
    ```cpp
    bool ledState = false;
    bool lastButtonState = HIGH;
    
    void loop() {
      bool currentButtonState = digitalRead(buttonPin);
      
      if (currentButtonState == LOW && lastButtonState == HIGH) {
        ledState = !ledState;
        digitalWrite(ledPin, ledState);
        delay(50); // Debounce
      }
      lastButtonState = currentButtonState;
    }
    ```

---

## Common Issues & Troubleshooting

### LED doesn't light up
- Check LED orientation (longer leg = positive)
- Verify resistor value (220Ω)
- Confirm pin connections

### Button doesn't respond
- Ensure INPUT_PULLUP is used or external pull-up resistor is connected
- Check button orientation on breadboard

---

## Next Steps

- [ ] Add multiple LEDs for a traffic light
- [ ] Use PWM for LED brightness control
- [ ] Add a buzzer for audio feedback
- [ ] Create a reaction time game

---

## Resources

- [Arduino Digital Pins](https://www.arduino.cc/reference/en/language/functions/digital-io/digitalread/)
- [Pull-up Resistors Explained](https://learn.sparkfun.com/tutorials/pull-up-resistors)
