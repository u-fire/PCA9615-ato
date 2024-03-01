# PCA9615

📒 [Datasheet](https://www.nxp.com/docs/en/data-sheet/PCA9615.pdf)

This repository is an [atopile](https://atopile.io/) module for the PCA9615, 2-channel multipoint Fast-mode differential I2C-bus. In other words, it lets
you use I2C like you might use MODBUS, ie. over long wires and through noisy environments. 

## 🏁 Get started
### Installation
From inside a project directory terminal: `ato install pca9615-ato`

### Code
```Go
from "pca9615/elec/src/pca9615.ato" import PCA9615
from "generics/interfaces.ato" import DiffPair, Power, I2C, GPIO

module Test:
    # define virtual buses, connect these to physical components/pins
    power = new Power
    d_sda = new DiffPair
    d_scl = new DiffPair
    i2c = new I2C
    power_a_2v3_5v5 = new Power
    power_b_3v0_5v5 = new Power
    differential_side_power = new Power
    en = new GPIO

    # define the PCA9615
    pca9615 = new PCA9615

    # PCA9615 enable pin is driven high be an internal pullup
    # to disable chip, drive low
    en ~ pca9615.en

    # connect power to normal I2C side
    power_a_2v3_5v5 ~ pca9615.power_a_2v3_5v5

    # differential power can be seperate or the same as the I2C side
    pca9615.power_b_3v0_5v5 ~ power_a_2v3_5v5
    # - or -
    pca9615.power_b_3v0_5v5 ~ differential_side_power

    # normal I2C connection
    i2c ~ pca9615.i2c

    # differential pairs
    d_sda ~ pca9615.d_sda
    d_scl ~ pca9615.d_scl
```

## 🤔 Design Considerations
- d_sda and d_scl must be routed as differential pairs (`6` in KiCAD PCB Editor)
- power on the I2C side can be separate from the differential side, common grounds are not required
- the I2C side can accept between 2.3 - 5.5 volts
- the differential side can accept 3.0 - 5.5 volts
- an RJ45 connector and CAT6 cable work well for making the connections

## 🔌 Connections

|MCU        |       | MCU PCA9615   |       |Sensor PCA9615     | | Sensor |
|-------:   |-:    |---:                  |:-:         |:---           |:-: | :-|
|  |      | MCU PCA9615.d_SDA 🟩|~            |🟩 PCA9615.d_SDA|~ | 
| |      | MCU PCA9615.d_SCL 🟪|~            |🟪 PCA9615.d_SCL|~
| MCU.SDA 🟨 |~      | 🟨 PCA9615.SDA 🟨|            |🟨 PCA9615.SDA|~ |🟨 Sensor.SDA
| MCU.SCL 🟦 |~      | 🟦 PCA9615.SCL 🟦|            |🟦 PCA9615.SCL|~ |🟦 Sensor.SCL
| *MCU.VCC* 🟥      | ~| 🟥 *PCA9615.VCC* 🟥 |*(optional)*|🟥 *PCA9615.VCC*|~ | 🟥 *Sensor.VCC*
|**MCU.GND**⬛      |~       | ⬛ **PCA9615.GND** ⬛|*(optional)*|⬛ **PCA9615.GND**|~|⬛ **Sensor.GND**


## ⚡ Programming
Once connected, the sensor (or just the other side) appear as a normal I2C device to the host. Nothing special needs to be done.

## 🙏 Contributing
This design is intended to be a community best-effort at a minimal circuit combining:
- datasheet reference design
- readily available components
- modular and reusable layout

You are greatly encouraged to contribute or discuss any improvements here so that everyone may benefit. 

## License
[CERN-OHL-P v2](https:/cern.ch/cern-ohl)