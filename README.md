# SIPP: Satellite Image Processing Pipeline

SIPP stands for **Satellite Image Processing Pipeline**. It is a modular, resource-aware image processing framework designed for the resource-constrained environment of a CubeSat, built on top of the CubeSat Space Protocol (CSP).

## Architecture Overview

SIPP is designed to be robust, modular, and adaptive.

*   **CSP-Based Core**: The pipeline operates as a CSP application (default address 162), accessible over ZMQ, CAN, or KISS interfaces.
*   **Modular Design**: Processing stages are dynamically loaded shared objects (`.so`), allowing for flexible reconfiguration of the pipeline without recompiling the core.
*   **Flexible Memory Architecture**: The pipeline supports both standard in-memory processing for maximum performance and memory-mapped (mmap) processing for enhanced durability and reliability, ensuring data persistence.
*   **Efficient Data Flow**: Image batches and metadata are efficiently passed between modules, leveraging the chosen memory backend to minimize overhead.
*   **Adaptive Heuristics**: The system includes smart heuristics (e.g., Best Effort, Lowest Effort) to manage processing loads based on available power and thermal constraints.
*   **Protobuf Configuration**: Pipeline and module configurations are managed via Protocol Buffers, ensuring type-safe and compact configuration updates.

## Prerequisites

To build SIPP, the following packages are required:

```bash
sudo apt install \
    libcurl4-openssl-dev git build-essential libsocketcan-dev can-utils \
    libzmq3-dev libyaml-dev pkg-config fonts-powerline python3-pip \
    libelf-dev libbsd-dev libprotobuf-c-dev brotli libbrotli-dev

sudo pip3 install meson ninja
```

## Building the Pipeline

SIPP uses the Meson build system.

```bash
./configure
```

This script crosscompile the pipeline for using the Yocto toolchain.

## Running SIPP

The executable is currently named `dipp`. Run it from the build directory:

```bash
./builddir/dipp [OPTIONS]
```

### Command Line Arguments

| Flag | Default | Description |
| :--- | :--- | :--- |
| `-i` | `ZMQ` | Connection interface (`ZMQ`, `KISS`, `CAN`). |
| `-p` | `localhost` | Port or device path (e.g., `/dev/ttyS1` for KISS, `vcan0` for CAN). |
| `-a` | `162` | CSP node address for the SIPP application. |
| `-t` | *(none)* | Enable tracing to a file (e.g., `-t trace.json`). |

## Configuration & Operation

Pipeline configuration is handled via the [CSP Parameter System](https://github.com/libcsp/libparam).

*   **Modules**: Place compiled modules in `external_modules/`.
*   **Dynamic Updates**: Use the `ippc` CSH extension (available in `csp_ippc`) to update module parameters/configs.
    *   `ippc module [options] <module-idx> <config-file>`
    *   `ippc pipeline [options] <pipeline-idx> <config-file>`

All configurations are persistently stored in VMEM.

## Development

*   **Modules**: See [DISCO-2 module template](https://github.com/Lindharden/DISCO2-module-template).
