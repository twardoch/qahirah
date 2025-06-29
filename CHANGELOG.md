# Changelog

All notable changes to the Qahirah project will be documented in this file.

## Recent Changes

### 2025-06-25
- **Auto-commit**: Large-scale code reformatting (4729 insertions, 4356 deletions)
  - Reformatted qahirah.py and setup.py for improved code consistency

### 2017-11-22
- **Cross-platform support**: Added dynamic library loading for macOS and Windows
  - Added platform detection using sys.platform
  - Windows: libcairo-2.dll, freetype6.dll, libfontconfig-1.dll
  - macOS: libcairo.2.dylib, libfreetype.6.dylib, libfontconfig.1.dylib
  - Linux: libcairo.so.2, libfreetype.so.6, libfontconfig.so.1 (unchanged)

### Earlier Updates
- **Path enhancements**: Added Path.from_tuple method for easier path creation
- **Documentation**: Removed note about reference circularities (resolved)
- **XCB support**: Added low-level XCB (X protocol C-language Binding) support
- **Fontconfig integration**: Alternative FontFace.create_for_pattern using Fontconfig wrapper when available
- **FreeType improvements**: 
  - Added FreeType lock/unlock calls for thread safety
  - Added notes about missing FreeType support areas
- **API improvements**: 
  - Vector.axis_swap() now defaults swap argument to True
  - Added Python version check for compatibility
- **Legal**: Added copy of LGPL license
- **Stability fixes**: 
  - Removed __del__ methods to fix segfaults at process termination
  - Fixed user_data leakage hole
  - Added weakrefs for Devices

## Version History

### v0.91 (Current)
- Python 3.3+ language bindings for Cairo graphics library
- Higher-level API with Vector class for 2D operations
- Property-based API (read/write properties instead of get/set methods)
- Method chaining support
- FreeType font support with optional python_freetype integration
- User fonts, Script surfaces, and ScaledFont.text_to_glyphs support