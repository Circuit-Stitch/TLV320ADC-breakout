# Bill of Materials

Quantities are for one board. Stock and prices were checked on 2026-10-12. A dash means not yet priced.

Status: **Chosen** = part number fixed. **Candidate** = likely, not confirmed. **TBD** = not picked.

Digi-Key prices marked `Digi-Key*` come from Digi-Key's USD mirror site, which shows no stock and may be stale. Stock for those parts comes from digikey.ca. Every Digi-Key listing carries a possible US tariff. Mouser could not be reached. LCSC prices are live USD.

The support parts follow the Ensor-384 board, dated 2024-11-13.

## Parts

Power comes from the Pico 2 header. 5 V feeds the AVDD regulator. The Pico 3.3 V rail feeds IOVDD and the oscillator, as on Ensor.

| Ref       | Item                    | Part                                              | Qty | Status    | Price at 1 | Price at 100 | Source    | Notes                                                                                                                                       |
|-----------|-------------------------|---------------------------------------------------|-----|-----------|------------|--------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| U1        | Audio ADC               | TI TLV320ADC6140IRTWR, WQFN-24 4×4 mm             | 1   | Chosen    | $7.53      | $4.89        | Digi-Key  | 744 in stock at Digi-Key, 12-week lead time. Owner confirmed on Digi-Key US. LCSC C1852020 has 1 at $11.15. TI store out of stock. I2C 0x4C with ADDR0 and ADDR1 grounded  |
| U2        | AVDD regulator          | TI TPS7A2033PDBVR, 3.3 V, SOT-23-5                | 1   | Candidate | $0.21      | $0.16 at 50  | LCSC      | C2862740, 178,020 in stock. Low noise, 300 mA. Digi-Key has 0 until 2026-11-23. Ensor uses TPS73533. Backup: LP5907MFX-3.3/NOPB             |
| Y1        | Audio reference         | Abracon ASTX-H11-24.576MHZ-T, 3.2×2.5 mm          | 1   | Chosen    | $4.37      | $3.30        | Digi-Key* | ±2.5 ppm TCXO. 556 in stock, 16-week lead time. JLCPCB C1987701. Standby pin tied to 3.3 V. Same part as Ensor. See [`docs/oscillator-selection.md`](https://github.com/Circuit-Stitch/portacorder/blob/main/docs/oscillator-selection.md) in portacorder                                 |
| FB1       | Oscillator supply bead  | Murata BLM18PG221SN1D, 0603                       | 1   | Candidate | $0.10      | $0.04        | Digi-Key* | 220 Ω at 100 MHz. Isolates the oscillator from the Pico 3.3 V rail. 235,085 in stock                                                        |
| R1        | SHDNZ pull-down         | Yageo RC0402FR-0710KL, 10 kΩ                      | 1   | Chosen    | —          | $0.01        | Digi-Key  | Holds the ADC in shutdown until the Pico drives SHDNZ high. Meets the power-up sequence. Same as Ensor                                      |
| R2        | MCLK series             | Yageo RC0402FR-0722RL, 22 Ω                       | 1   | Candidate | —          | —            | —         | Oscillator to GPIO1. Same value as Ensor                                                                                                    |
| R3–R5     | Audio bus series        | Yageo RC0402FR-0733RL, 33 Ω                       | 3   | Candidate | —          | $0.004       | LCSC      | R3 FSYNC, R4 BCLK, R5 SDOUT. Ensor has none. Added for the jumper wires to the Pico. Swap for 0 Ω if not needed                                      |
| R6, R7    | I2C pull-ups            | Yageo RC0402FR-074K7L, 4.7 kΩ                     | 2   | Candidate | —          | $0.01        | Digi-Key  | Digi-Key has 0 until 2026-11-09. Any 4.7 kΩ 1% 0402 fits                                                                                    |
| JP1, JP2  | Address jumpers         | Solder jumper, default to GND                     | 2   | Chosen    | —          | —            | —         | ADDR0 and ADDR1. Cut to change the I2C address                                                                                              |
| C1        | LDO input               | Kyocera AVX 06033C105KAT4A, 1 µF 25 V X7R 0603    | 1   | Chosen    | $0.40      | $0.152       | Digi-Key  | 12,980 in stock                                                                                                                             |
| C2        | AVDD                    | Kyocera AVX 06033C105KAT4A, 1 µF 25 V X7R 0603    | 1   | Chosen    | $0.40      | $0.152       | Digi-Key  | Also serves as the LDO output cap                                                                                                           |
| C3, C5, C7, C9 | Pin decoupling     | Murata GRM155R71C104KA88D, 100 nF 16 V X7R 0402   | 4   | Chosen    | $0.12      | $0.017       | Mouser    | AVDD, AREG, DREG, IOVDD. Price from the warm-dimmer BOM. LCSC C71629 is $0.005 at 100                                                       |
| C4, C6, C8, C10 | Bulk                | Murata GRT188R61A106KE13D, 10 µF 10 V X5R 0603    | 4   | Chosen    | $0.34      | $0.128       | Mouser    | AREG, DREG, IOVDD, VREF. Replaces GRM188R61A106KE69D, limited availability, as in the sister projects. LCSC C77044 still lists that part at $0.028. Ensor uses 10 µF on VREF; the datasheet minimum is 1 µF |
| C11       | MICBIAS                 | Kyocera AVX 06033C105KAT4A, 1 µF 25 V X7R 0603    | 1   | Chosen    | $0.40      | $0.152       | Digi-Key  | Fitted even with MICBIAS unused. Ground terminal straight to AVSS, no via                                                                   |
| C12       | Oscillator, HF          | Murata GRM155R71C103KA01D, 10 nF 16 V X7R 0402    | 1   | Chosen    | $0.11      | $0.014       | Mouser    | Price from the warm-dimmer BOM. Same value as Ensor                                                                                         |
| C13       | Oscillator, bulk        | Kyocera AVX 06033C105KAT4A, 1 µF 25 V X7R 0603    | 1   | Candidate | $0.40      | $0.152       | Digi-Key  | After FB1                                                                                                                                   |
| C14–C21   | Input coupling          | Kyocera AVX 0805ZC475KAT2A, 4.7 µF 10 V X7R 0805  | 8   | Candidate | —          | —            | —         | One per INxP and INxM. Same as Ensor. TI recommends low voltage coefficient for best distortion; see note below                            |
| J1        | Power and control       | 1×8 pin header, 2.54 mm                           | 1   | TBD       | —          | —            | —         | 5 V, 3.3 V, GND, SDA, SCL, SHDNZ, GND, GND                                                                                                  |
| J2        | Audio bus               | 1×6 pin header, 2.54 mm                           | 1   | TBD       | —          | —            | —         | FSYNC, GND, BCLK, GND, SDOUT, GND. Matches the U1 pin order. A ground next to each signal                                                                             |
| J3–J6     | Analog inputs           | 1×3 pin header, 2.54 mm                           | 4   | TBD       | —          | —            | —         | INxP, INxM, GND per channel. Pigtail to TRS for the MOTU                                                                                    |
| TP1–TP3   | Test points             | MCLK, AVDD, GND                                   | 3   | TBD       | —          | —            | —         | Scope access                                                                                                                                |
| —         | PCB                     | 4-layer, 1 oz                                     | 1   | Candidate | —          | —            | —         | Solid ground plane under U1. Thermal pad vias to ground                                                                                     |

Estimated parts cost at quantity 1 is about $18, before headers and the PCB. The ADC and oscillator are $11.90 of that. Capacitor prices come from the `warm-dimmer` and `optivisor_light` BOMs, checked 2026-10-07 to 2026-10-12.

## Input coupling capacitors

TI recommends coupling capacitors with a low voltage coefficient. X7R capacitance changes with voltage and can add distortion. C0G holds its value, but no 1 µF C0G 1206 from TDK or Murata could be confirmed in stock. The Panasonic ECP-U1C105MA5 film part is obsolete.

This board uses Ensor's 4.7 µF X7R parts. Rev 1 tests function, not distortion.

The TLV quick-charge default assumes coupling caps up to 1 µF. With 4.7 µF, firmware must set a longer quick-charge time.

Firmware should set the input impedance to 10 kΩ. With 4.7 µF, that gives a 3.4 Hz high-pass corner. The 2.5 kΩ default gives 14 Hz.
