# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

mpv is a free, open-source media player for the command line. It supports a wide variety of media file formats, audio and video codecs, and subtitle types. The project is written primarily in C and uses the Meson build system.

## Build System and Commands

### Building mpv
mpv uses Meson as its build system. Essential commands:

```bash
# Setup build directory
meson setup build

# Compile the project
meson compile -C build

# Install (optional)
meson install -C build

# View build options
meson configure build
```

### Testing
Tests are available but not enabled by default. To enable and run tests:

```bash
# Enable tests during setup
meson setup build -Dtests=true

# Run tests
meson test -C build

# Run specific test suites
meson test -C build --suite=libmpv
meson test -C build --suite=ffmpeg
```

### Development Tools
- Pre-commit hooks are configured in `.pre-commit-config.yaml`
- Code quality checks include: case conflict, executables with shebangs, JSON/YAML validation, codespell
- Lua checking is configured in `.luacheckrc`
- Swift linting configuration in `.swiftlint.yml`

## Architecture Overview

### Core Components

**Player Core (`player/*.c`)**
- `player/main.c`: Entry point and main playback loop
- `player/core.h`: Central data structures and function prototypes
- `player/command.c`: Implementation of client API commands and properties
- `player/client.c`: Client API (mpv/client.h) implementation
- Main state is stored in `MPContext` structure

**Key Subsystems:**
- `audio/`: Audio processing, output, and format handling
- `video/`: Video decoding, filtering, and output handling
- `demux/`: Media file parsing and stream demultiplexing
- `stream/`: Network and file I/O handling
- `sub/`: Subtitle rendering and processing
- `options/`: Configuration and option parsing
- `input/`: Keyboard and mouse input handling
- `misc/`: Utility functions and common code
- `osdep/`: Operating system dependent code

**Memory Management:**
- Uses talloc-inspired hierarchical memory management (`ta/`)
- Set `MPV_LEAK_REPORT=1` environment variable to detect memory leaks
- Parent-child allocation relationships for automatic cleanup

### Build Configuration

**Key Files:**
- `meson.build`: Main build configuration
- `meson.options`: Build options and feature toggles
- Essential dependencies: FFmpeg libraries, libplacebo, libass, zlib

**Important Options:**
- `-Dgpl=true/false`: Controls GPL vs LGPL licensing
- `-Dtests=true/false`: Enable/disable test building
- `-Dlibmpv=true/false`: Build libmpv library
- `-Dcplayer=true/false`: Build mpv CLI player

### Directory Structure

- `player/`: Core player logic and main loop
- `audio/`, `video/`: Media processing pipelines
- `demux/`, `stream/`: Input handling and parsing
- `sub/`: Subtitle processing
- `input/`: User input handling
- `options/`: Configuration system
- `osdep/`: Platform-specific code
- `misc/`, `common/`: Shared utilities
- `ta/`: Memory management
- `test/`: Unit tests and test utilities
- `DOCS/`: Documentation and technical references
- `etc/`: Configuration files and shell completions
- `TOOLS/`: Development and maintenance scripts

## Development Guidelines

### Licensing
- New code must be LGPLv2.1+ licensed (or compatible)
- GPL code allowed when `-Dgpl=true` is used
- Copyright belongs to contributors
- No fake names; pseudonyms are acceptable if contactable

### Contribution Process
- Use GitHub pull requests for code review
- Contact developers on IRC: #mpv and #mpv-devel on Libera.chat
- Follow coding standards and test changes
- Mark experimental changes as "[RFC]"

### Key Documentation
- `DOCS/tech-overview.txt`: Detailed architecture overview
- `DOCS/contribute.md`: Contribution guidelines
- `DOCS/client-api-changes.rst`: Client API changelog
- `DOCS/interface-changes.rst`: Player interface changes

## Common Development Tasks

### Running Tests
Individual test executables are built in the build directory when tests are enabled:
```bash
# Run individual tests
./build/test/chmap
./build/test/json
./build/test/timer
```

### Working with libmpv
When developing with the libmpv API:
- Header files are in `include/mpv/`
- Test programs in `test/libmpv_*.c` demonstrate usage
- API changes documented in `DOCS/client-api-changes.rst`

### Configuration and Options
- Default config in `etc/mpv.conf`
- Input bindings in `etc/input.conf`
- Build options in `meson.options`
- Runtime options defined in `options/options.c`