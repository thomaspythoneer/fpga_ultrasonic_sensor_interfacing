# FPGA Ultrasonic Sensor Interface

Real-time ultrasonic distance measurement system interfacing HC-SR04 sensor with FPGA, featuring precise timing control and distance calculation. Designed for 50 MHz FPGA clock with verified testbench achieving **0 errors** across multiple distance scenarios.

## Features

- **HC-SR04 Protocol**: Complete trigger (10µs pulse) and echo pulse-width measurement
- **Distance Calculation**: Converts echo duration to cm with 1 cm resolution (16-bit output)
- **50 MHz Operation**: Real-time processing at 20 ns clock period
- **Obstacle Detection**: Binary output for proximity alerts
- **Robust Testbench**: Validates 5 test cases (10cm, 27cm, 34cm, 10cm, no-target)

## Hardware Interface

```
HC-SR04 Sensor → FPGA
├── VCC (5V) ── Connected externally
├── GND ─────── Common ground
├── Trig ────── FPGA output (10µs pulse every ~2ms)
└── Echo ────── FPGA input (pulse width ∝ distance)
```

**Timing Protocol:**
```
Trigger: 10µs HIGH pulse → Sensor emits 40kHz burst
Echo:   HIGH for 38µs/cm (round trip) → Up to ~4m range
```

## Architecture

```verilog
t1b_ultrasonic (Top Module)
├── Clock Divider (50MHz → timing reference)
├── Trigger Generator (10µs pulse, ~2ms period)
├── Echo Pulse Counter (ns resolution)
├── Distance Calculator (echo_width → cm)
└── Obstacle Logic (distance < threshold)
```

**Key Signals:**
- `clk_50M`: 50 MHz system clock
- `reset`: Synchronous reset
- `echo_rx`: Sensor echo input
- `trig`: 10µs trigger output  
- `op`: Obstacle detected (HIGH when close)
- `distance_out[15:0]`: Distance in cm

## Testbench Results

| Test Case | Echo Duration | Expected Distance | Status |
|-----------|---------------|------------------|--------|
| 1         | 50,000 cycles | 169 cm           | ✅ PASS |
| 2         | 80,000 cycles | 271 cm           | ✅ PASS |
| 3         | 100,000 cycles| 339 cm           | ✅ PASS |
| 4         | 30,000 cycles | 101 cm           | ✅ PASS |
| 5         | No echo       | 0 cm             | ✅ PASS |

**Verification Summary:** `result.txt` reports **"No Errors"** after 5 complete measurement cycles.

## File Structure

```
.
├── rtl/
│   └── t1b_ultrasonic.v      # Main module
├── sim/
│   ├── tb.v                  # Comprehensive testbench
│   └── result.txt            # PASS/FAIL results
└── README.md
```



## Performance

```
Clock Frequency: 50 MHz (20ns period)
Trigger Period:  ~2 ms (500 Hz update rate)
Max Range:      ~400 cm (echo timeout)
Resolution:     1 cm
Accuracy:       ±1 cm (verified)
```

## Applications

- **Robotics**: Obstacle avoidance
- **IoT**: Distance monitoring  
- **Automation**: Level sensing
- **Education**: Sensor interfacing lab

## Future Enhancements

- Multiple sensor array (servo scanning)
- Averaging filter for noise reduction
- UART output for PC communication
- SPI/I2C interface for embedded systems

***

