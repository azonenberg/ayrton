# ayrton
Many-channel low speed high bit depth oscilloscope/DAQ

Project namesake: https://en.wikipedia.org/wiki/Hertha_Marks_Ayrton

High level goal: 1U gizmo with as many front panel probe inputs as we can. Extreme dynamic range and low sample rate,
one primary goal is monitoring power supply behavior during startup so we want the dynamic range to simultaneously see
startup ramps from zero and switching transients / control loop response.

## Tentative architecture

### Digital board

#### Bandwidth requirements

Suppose we use four analog boards (16 channels)

At 100 Msps, each ADC produces 1.6 Gbps of data
total 1.6 * 16 = 25.6 Gbps bandwidth

#### FPGA

Proposal: XC7K160T-2FFG676C because I have a bunch floating around

250 HR + 150 HP IO, 8x GTX

Allocate ~all of the HPIO to a DDR3 SODIMM

At 800 MT/s (conservative), 64 bit bus is 51.2 Gbps theoretical throughput

So we'd need 50% throughput to run four ADCs which should be very comfortable

This leaves HR IOs largely unallocated

#### Main CPU

STM32MP257 TFBGA436 as main processor to test how it works

Connect FPGA and MCU via PCIe gen2 as primary intended interface (5 GT/s)

#### Transceiver allocations

CPLL tops out at 6.6 Gbps

So we need to use QPLL for 8 Gbps... Hmmmm

If we run one QPLL at 10.3125 Gbps for 10GbE then we can't use that one for JESD204

5 Gbps PCIe can fit wherever using CPLL

### Analog board

AD9656 + TBD frontend

4-lane JESD204B but can run in single lane mode. Data rate tops out at 8 Gbps per lane so we can use any convenient
FPGA.

Total data rate = 4 converters * 16 bits * (10/8) coding overhead * 100 Msps = 8000 Mbps max SERDES rate. We cannot run
the ADC at 125 Msps if all four are active but could potentially run 2 out of every 4 at 125. Probably not worth the
hassle to only get a 25% sample rate boost?

Signals we need per ADC
* 1x JESD204 RX pair to SERDES
* CLK (LVDS)
* SYSREF (LVDS)
* SYNC (LVDS)
* SPI (3 pin)
* Power down
* SYNC
* Power obviously

Plus whatever else the frontend requires

So total of four high speed diff pairs, 3 out and 1 in, and some low speed control stuff

PicoBlade can handle 2.5A (26AWG) to 1.5A (32AWG) for 2-circuit, down to 1A - 0.8A for 15 circuit
Can we supply power + SPI or similar control bus over PicoBlade then have a 4-diffpair high speed connection?

For high speed
	Samtec ARF6 series for JESD204 + refclk
	PicoBlade for power and i2c/spi/something for control plane
