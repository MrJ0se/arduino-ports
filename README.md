# Arduino port for common libraries:
To use or contribute, keep in mind: this project remove all file system functions, and assembly dependences/optimizations, the unique focus is portability.

- require Cmake with target system toolchain (arduino core is not required).
- Tested with avr toolchain from Arduino IDE avr tools (a simple cmake configuration for avr-gcc and avr-g++ compilers).

## Status:
- ✅ tested
- 🔨 build
- ❌ fail
- ⚙️ fail, but rest some tries

| | ![Windows](./ard.png) | obs. |
| :---: | :---: | :--- |
| **Basic**      | -- | |
| zlib           | 🔨 | removed GZ file utilities |
