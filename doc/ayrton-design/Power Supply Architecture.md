## SoC

### Nominal rail voltages
* VDD: 1.8 or 3.3
* VDDIOx: 1.8 or 3.3
* VDDA18AON: 1.8
* VDDcore/csi/ds/lvds/combophy/pcieclk: 0.82 nominal, can drop to 0.67 in low power
* VddCPU: 0.8 nominal, can boost to 0.91 in overdrive or dip to 0.67 in low power
* Vddgpu: 0.8 nominal, can boost to 0.90 in overdrive
* Vdda18*: 1.8
* Vdd33*: 3.3
* Vref+: 1.1 - 1.8 nominal 1.25 or 1.5
* Vbat: 2.3 - 3.6
* Vddqddr: 1.1 assuming LPDDR4
### Sequencing constraints
* See datasheet 6.3.2
* VDDA18ON and VDD must ramp simultaneously (within 1ms)
* VDDA18ON and VDD must be first rails energized (other than VBAT)
* VDD33USB must not be on without VDDA18USB
* VDDA18USB and VDDA33USB without VDDCORE causes high leakage
### Max current (with 1.2 GHz non-overdrive, worst case Tj)
* Vddcore: 1.2A
* Vddcpu: 410 mA
* Vddgpu: 370 mA
* Vdd: 3.7 mA (all periph disabled?)
* Vdda18: 4.9 mA
* Vdda18on: 5.1 mA
### DDR4
* Burst read:
	* VDD1 = 3.13 mA
	* VDD2 = 288.95 mA
	* VDDQ = 81.07 mA
* All bank refresh burst
	* VDD1 = 11.59 mA
	* VDD2 = 110.08 mA
	* VDDQ = 0.82 mA
* VDD1 = 1.8
* Vdd2, Vddca, Vddq = 1.1