# Load Cell Weighing Board (ATmega328P + HX711)

A custom PCB design for measuring the weight of food dispensed into a pan, designed in EasyEDA. A 5 kg single-point strain gauge load cell feeds an HX711 24-bit ADC, and an ATmega328P-AU reads and processes the result on the same board.

> Originally made for an electronics assignment: *design a system to measure the weight of food being dispensed into a pan using an appropriate load cell, ADC, connectors and an MCU.*

## Block diagram

```
Load cell (E+ E- S+ S-) --> Screw terminal --> HX711 (24-bit ADC + PGA) --DOUT/PD_SCK--> ATmega328P-AU --> output
                                                                                          ^
                              5 V DC jack --> bulk + decoupling caps --------------------+
                              ISP header ----> programming
```

## Design choices

### Load cell: single-point strain gauge, 5 kg
- Strain gauge and capacitive cells are the two types accurate and sensitive enough for dispensing; strain gauge is the standard in weighing and food packaging.
- **Single-point** cells give correct readings even when the load is off-centre, which suits a pan that food lands in unevenly.
- Capacity is chosen slightly above pan weight plus the maximum food portion, so the cell stays in its working range without risk of overload.
- Rigid construction gives good resistance to mechanical disturbance.

### ADC: HX711 (delta-sigma, 24-bit)
- Load cell output is only a few mV, so it needs amplification. The HX711 combines a programmable-gain amplifier with a delta-sigma ADC.
- Oversampling and built-in digital filtering reduce noise.
- 24-bit resolution captures small weight changes.

### Connectors and cabling
- 4-way **screw terminal** for the load cell: E+, E-, S+, S-. No special tools needed and a secure connection.
- **Shielded 4-core cable** to cut EMI/RFI pickup. Double-shielded cable is costlier and stiffer, and is mainly worth it for long runs.
- HX711 to MCU is digital, so it is far less noise-sensitive: plain PCB traces when the MCU is on the board, jumper wires if an external MCU is used.
- Decoupling capacitors at supply pins to suppress noise and voltage spikes.

## Hardware

| Part | Ref | Role |
|---|---|---|
| ATmega328P-AU (TQFP-32) | U2 | Microcontroller |
| HX711 | U1 | 24-bit ADC / amplifier |
| Screw terminal (4-way) | J1 | Load cell: E+, E-, S+, S- |
| DC005 2.0 mm barrel jack | DC1 | 5 V input (**5 V only**) |
| 2x3 header | U3 | ISP programming |
| 10 uF | C3 | Bulk supply capacitor |
| 0.1 uF | C1, C2, C4, C5 | Decoupling |
| 10 k | R1 | Reset pull-up |

## Status

**Design stage.** The schematic and PCB layout are complete. The board has not been fabricated or tested yet, so there are no measured results.

## Planned bring-up and testing
1. Check VCC and GND for shorts, then power with 5 V and measure the rails.
2. Flash the ATmega328P over the ISP header.
3. Read the HX711 and check that the raw value changes with load.
4. Calibrate with known masses and record the error.
5. Measure noise (unloaded) and zero drift over time.

## Possible improvements
- Add a UART header and status LED for readout and debugging.
- Add a TVS diode and ferrite bead on the load cell inputs.
- Use a low-noise LDO for the bridge excitation.
- Consider a lower-noise ADC (e.g. ADS1232) if the HX711's noise limits portion accuracy.
- Add a footprint for a crystal if timing accuracy matters.

## Tools
EasyEDA (schematic and layout).
