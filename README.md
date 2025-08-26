# Heimdall

[![build](https://github.com/JingMatrix/Heimdall/actions/workflows/build.yml/badge.svg)](https://github.com/JingMatrix/Heimdall/actions/workflows/build.yml)

Heimdall is a cross-platform open-source tool suite used to flash
firmware (aka ROMs) onto Samsung mobile devices.

## How does Heimdall work?

Heimdall connects to a mobile device over USB and interacts with
low-level software running on the device, known as Loke. Loke and
Heimdall communicate via the custom Samsung-developed protocol
typically referred to as the 'Odin 3 protocol'.

USB communication in Heimdall is handled by the popular open-source
USB library, [libusb](https://libusb.info).

## Free & Open Source

Heimdall is both free and open source. It is licensed under the MIT
license (see LICENSE).

Heimdall has been developed through countless hours of reverse
engineering work, predominantly by [Glass
Echidna](https://glassechidna.com.au/), a _tiny_ independent software
development company.

## Documentation

To compile Heimdall and Heimdall-frontend (the gui application), run
something like:

```sh
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

To only compile the CLI tool, add the option -DDISABLE_FRONTEND=true
to the cmake command.

The name of dependencies vary between distributions. On alpinelinux
you need to install: `make cmake gcc g++ libc-dev qt6-qtbase-dev and
libusb-dev`, on archlinux: `cmake libusb qt6-base` and on ubuntu: `cmake
g++ pkg-config libusb-1.0.0-dev qt6-base-dev and zlib1g-dev`.

Some more documentation, and instructions on how to use Heimdall and
Heimdall-frontend, can be found in the doc/ folder.

### Odin protocol and PIT format

For more details on the Odin protocol, and the PIT files, see the
external project [samsung-loki/samsung-docs](https://samsung-loki.github.io/samsung-docs/).
