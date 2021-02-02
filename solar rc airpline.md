
 * [[Solar Panel]]
 * [[Aerodynamics]]

The goal of this project is to build a Solar-powered RC Airplane with autonomous capabilities. Ultimately, the solar panels should be able to slowly recharge the batteries at crusing speed during solar noon. 

## Goals ##

 * Solar panels provide enough power to maintain cruising speed during solar noon. Extra power recharges batteries
 * Connect with laptop ground station running [APM Planner 2](https://ardupilot.org/planner2/index.html) software during flight to provide flight data and set waypoints.
 * Send FPV (First Person Video) video to ground with camera
 * Use open-source software and hardware. Document development process and eventually open-source any software and hardware so others can replicate

## Ground Station

#### [Telemetry Radio](https://ardupilot.org/plane/docs/common-telemetry-landingpage.html)

The airplaine will communicate to the ground station via a [SiK Telemetry Radio](https://ardupilot.org/copter/docs/common-sik-telemetry-radio.html#common-sik-telemetry-radio). The Radio uses the [MavLink](https://mavlink.io/en/) protocol. MavLink is an open-source protocol. The SiK radio is built around the [Silicon Labs Si1000](https://media.digikey.com/pdf/Data%20Sheets/Silicon%20Laboratories%20PDFs/Si1000-5.pdf).

https://www.youtube.com/watch?v=Rv1MVkZuGbg

https://www.youtube.com/playlist?list=PLoPtpxJIxgnYnPrOeGHs3rdhhPgNGIYN5

#### FPV Camera / OSD (On Screen Display) 

Based around the [AT7456E](https://www.sparkfun.com/datasheets/BreakoutBoards/MAX7456.pdf)

#### Receiver

https://fishpepper.de/projects/usky/

# Betaflight

Betaflight is an open source flight controler.

# ArduPilot

Open source RC flight controller software.

 * [Porting ArduPilot](https://ardupilot.org/dev/docs/porting.html)

# Open-source flight controllers
https://ardupilot.org/plane/docs/common-autopilots.html

 * [Pixracer](https://ardupilot.org/plane/docs/common-pixracer-overview.html)
 * [OpenPilot](https://ardupilot.org/plane/docs/common-openpilot-revo-mini.html)

# Charge controller options

Really need a bi-directional Dc Dc converter

 * [LT3652](https://www.analog.com/media/en/technical-documentation/data-sheets/3652fe.pdf) - uses temperature-compensated MPPT
 * [LT8491](https://www.analog.com/media/en/technical-documentation/data-sheets/LT8491.pdf) - badass MPPT i2c
 * [CN3791](http://www.consonance-elec.com/pdf/datasheet/DSE-CN3791.pdf) - super cheap battery charger with voltage MPPT
 * LT3780 DC converter


https://www.youtube.com/watch?v=o9BOrAHH5E4

# Similar Projects

https://github.com/vladisenko/EachiWhoop
