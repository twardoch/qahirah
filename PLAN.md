# Qahirah Improvement Plan

## Executive Summary

Qahirah is a Python 3 wrapper for the Cairo graphics library that offers a more Pythonic API than the standard Pycairo. While the library is functional and provides good abstractions (Vector class, property-based API, method chaining), it suffers from several architectural and modern Python development practice issues that limit its stability, maintainability, and ease of deployment.

This document outlines a comprehensive plan to modernize and improve Qahirah, making it more robust, elegant, and easier to deploy/install.

## Current State Analysis

### Strengths
- Clean, Pythonic API design with Vector class abstraction
- Property-based interface instead of getter/setter methods
- Method chaining support for fluent interfaces
- Cross-platform support (Windows, macOS, Linux)
- Comprehensive Cairo feature coverage including user fonts and script surfaces
- Good memory management patterns using weak references

### Critical Issues
1. **Monolithic Architecture**: Everything in a single 7,766-line file
2. **No Testing**: Complete absence of any test suite
3. **No Type Hints**: Missing type annotations for modern Python development
4. **Poor Error Handling**: Limited exception handling and recovery
5. **No Thread Safety**: Cairo requires thread safety considerations not addressed
6. **Outdated Tooling**: Uses distutils instead of modern setuptools/poetry
7. **Limited Documentation**: No comprehensive API documentation or examples

## Improvement Strategy

### Phase 1: Foundation and Stability (Weeks 1-2)

#### 1.1 Project Structure Modernization
**Goal**: Establish a modern Python project structure

**Implementation**:
```
qahirah/
├── src/
│   └── qahirah/
│       ├── __init__.py          # Main exports and version
│       ├── core/
│       │   ├── __init__.py
│       │   ├── vector.py        # Vector class
│       │   ├── matrix.py        # Matrix class
│       │   ├── rect.py          # Rect class
│       │   └── colour.py        # Colour class
│       ├── cairo/
│       │   ├── __init__.py
│       │   ├── bindings.py      # Low-level Cairo bindings
│       │   ├── constants.py     # CAIRO class constants
│       │   └── errors.py        # Error handling
│       ├── context.py           # Context class
│       ├── surfaces/
│       │   ├── __init__.py
│       │   ├── base.py          # Surface base class
│       │   ├── image.py         # ImageSurface
│       │   ├── pdf.py           # PDFSurface
│       │   ├── ps.py            # PSSurface
│       │   ├── svg.py           # SVGSurface
│       │   └── recording.py     # RecordingSurface
│       ├── patterns/
│       │   ├── __init__.py
│       │   ├── base.py          # Pattern base class
│       │   └── mesh.py          # MeshPattern
│       ├── fonts/
│       │   ├── __init__.py
│       │   ├── face.py          # FontFace classes
│       │   ├── scaled.py        # ScaledFont
│       │   └── options.py       # FontOptions
│       ├── drawing/
│       │   ├── __init__.py
│       │   ├── path.py          # Path class
│       │   └── region.py        # Region class
│       └── utils/
│           ├── __init__.py
│           ├── platform.py      # Platform-specific loading
│           └── weakrefs.py      # Weak reference utilities
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── examples/
├── docs/
├── pyproject.toml
├── setup.py (for compatibility)
├── README.md
├── CHANGELOG.md
├── LICENSE
└── tox.ini
```

**Steps**:
1. Create the new directory structure
2. Split qahirah.py into logical modules
3. Ensure backward compatibility with imports
4. Update imports to use relative imports within package

#### 1.2 Modern Build System
**Goal**: Replace distutils with modern Python packaging

**Implementation**:
1. Create `pyproject.toml` with:
   ```toml
   [build-system]
   requires = ["setuptools>=61.0", "wheel"]
   build-backend = "setuptools.build_meta"

   [project]
   name = "qahirah"
   version = "1.0.0"
   description = "Pythonic bindings for Cairo graphics library"
   requires-python = ">=3.8"
   dependencies = []
   optional-dependencies = {
     dev = ["pytest>=7.0", "pytest-cov", "mypy", "black", "ruff"],
     docs = ["sphinx", "sphinx-rtd-theme"],
   }
   ```

2. Keep minimal `setup.py` for backward compatibility
3. Add `MANIFEST.in` for non-Python files
4. Create `.gitignore` with standard Python patterns

#### 1.3 Type Annotations
**Goal**: Add comprehensive type hints throughout the codebase

**Implementation Strategy**:
1. Start with core classes (Vector, Matrix, Rect, Colour)
2. Add return type annotations to all methods
3. Add parameter type annotations
4. Use `typing.Protocol` for Cairo object interfaces
5. Create type stubs for external libraries if needed

**Example**:
```python
from typing import Union, Tuple, Optional, overload
from numbers import Real

class Vector:
    @overload
    def __init__(self, x: Real, y: Real) -> None: ...
    
    @overload
    def __init__(self, point: Tuple[Real, Real]) -> None: ...
    
    def __init__(self, x: Union[Real, Tuple[Real, Real]], y: Optional[Real] = None) -> None:
        # implementation

    def __add__(self, other: 'Vector') -> 'Vector': ...
    
    @property
    def x(self) -> Real: ...
    
    @property
    def y(self) -> Real: ...
```

### Phase 2: Testing and Quality (Weeks 3-4)

#### 2.1 Comprehensive Test Suite
**Goal**: Achieve >80% test coverage

**Test Structure**:
```
tests/
├── unit/
│   ├── test_vector.py
│   ├── test_matrix.py
│   ├── test_rect.py
│   ├── test_colour.py
│   └── test_context.py
├── integration/
│   ├── test_surfaces.py
│   ├── test_patterns.py
│   ├── test_fonts.py
│   └── test_drawing.py
├── performance/
│   └── benchmarks.py
└── conftest.py  # pytest fixtures
```

**Testing Approach**:
1. Unit tests for all mathematical operations (Vector, Matrix)
2. Integration tests for Cairo operations
3. Mock Cairo library calls where appropriate
4. Property-based testing for mathematical properties
5. Visual regression tests for drawing operations

**Example Test**:
```python
import pytest
from qahirah import Vector

class TestVector:
    def test_construction_from_components(self):
        v = Vector(3, 4)
        assert v.x == 3
        assert v.y == 4
    
    def test_construction_from_tuple(self):
        v = Vector((3, 4))
        assert v.x == 3
        assert v.y == 4
    
    def test_addition(self):
        v1 = Vector(1, 2)
        v2 = Vector(3, 4)
        result = v1 + v2
        assert result.x == 4
        assert result.y == 6
    
    def test_magnitude(self):
        v = Vector(3, 4)
        assert v.magnitude() == 5.0
```

#### 2.2 Error Handling Improvements
**Goal**: Robust error handling with informative messages

**Implementation**:
1. Create custom exception hierarchy:
   ```python
   class QahirahError(Exception):
       """Base exception for Qahirah"""
   
   class CairoError(QahirahError):
       """Cairo operation failed"""
       def __init__(self, status: int, operation: str):
           self.status = status
           self.operation = operation
           super().__init__(f"Cairo {operation} failed: {self.status_to_string(status)}")
   
   class InvalidParameterError(QahirahError):
       """Invalid parameter passed to function"""
   ```

2. Add context to all errors
3. Implement error recovery where possible
4. Add logging for debugging

#### 2.3 Thread Safety
**Goal**: Document and implement thread safety where needed

**Implementation**:
1. Add thread-local storage for Cairo contexts
2. Document thread safety guarantees
3. Add optional locking for shared resources
4. Create thread-safe factory methods

```python
import threading
from contextlib import contextmanager

class ThreadSafeContext:
    _lock = threading.Lock()
    _thread_local = threading.local()
    
    @classmethod
    @contextmanager
    def acquire(cls, surface):
        with cls._lock:
            if not hasattr(cls._thread_local, 'context'):
                cls._thread_local.context = Context(surface)
            yield cls._thread_local.context
```

### Phase 3: Documentation and Examples (Week 5)

#### 3.1 API Documentation
**Goal**: Complete Sphinx-based documentation

**Structure**:
```
docs/
├── source/
│   ├── conf.py
│   ├── index.rst
│   ├── quickstart.rst
│   ├── api/
│   │   ├── core.rst
│   │   ├── drawing.rst
│   │   ├── surfaces.rst
│   │   └── fonts.rst
│   ├── examples/
│   └── migration.rst  # From Pycairo
├── Makefile
└── requirements.txt
```

**Documentation Standards**:
1. All public APIs must have docstrings
2. Include parameter types and descriptions
3. Add usage examples in docstrings
4. Create tutorial-style guides

#### 3.2 Example Gallery
**Goal**: Comprehensive examples demonstrating all features

**Examples to Include**:
1. Basic shapes and paths
2. Gradients and patterns
3. Text rendering and fonts
4. Image manipulation
5. PDF/SVG generation
6. Animation basics
7. Complex compositions
8. Performance optimization

### Phase 4: Deployment and Distribution (Week 6)

#### 4.1 CI/CD Pipeline
**Goal**: Automated testing and deployment

**GitHub Actions Workflow**:
```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        python-version: ["3.8", "3.9", "3.10", "3.11", "3.12"]
    
    steps:
    - uses: actions/checkout@v3
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install system dependencies
      run: |
        # Install Cairo based on OS
    - name: Install package
      run: |
        pip install -e .[dev]
    - name: Run tests
      run: |
        pytest --cov=qahirah
    - name: Type checking
      run: |
        mypy src/qahirah
```

#### 4.2 Platform-Specific Improvements
**Goal**: Reliable installation across platforms

**Implementation**:
1. Better library detection:
   ```python
   def find_cairo_library():
       """Find Cairo library using multiple strategies"""
       strategies = [
           lambda: _find_using_pkg_config(),
           lambda: _find_in_standard_locations(),
           lambda: _find_using_ctypes_util(),
       ]
       
       for strategy in strategies:
           try:
               return strategy()
           except Exception:
               continue
       
       raise RuntimeError("Could not find Cairo library")
   ```

2. Create platform-specific wheel builders
3. Add installation troubleshooting guide
4. Support conda-forge distribution

#### 4.3 Performance Optimization
**Goal**: Identify and optimize performance bottlenecks

**Approach**:
1. Profile common operations
2. Optimize Vector/Matrix operations
3. Reduce Python/C boundary crossings
4. Add caching where appropriate
5. Consider Cython for critical paths

### Phase 5: Advanced Features (Weeks 7-8)

#### 5.1 Context Managers
**Goal**: Pythonic resource management

**Implementation**:
```python
from contextlib import contextmanager

@contextmanager
def cairo_context(surface):
    """Context manager for Cairo operations"""
    ctx = Context(surface)
    try:
        yield ctx
    finally:
        ctx.finish()

# Usage
with cairo_context(surface) as ctx:
    ctx.rectangle(0, 0, 100, 100)
    ctx.fill()
```

#### 5.2 Async Support
**Goal**: Support for async/await patterns

**Implementation**:
1. Async surface creation
2. Async file I/O for PDF/SVG
3. Thread pool for CPU-intensive operations

#### 5.3 Plugin System
**Goal**: Extensible architecture

**Implementation**:
1. Plugin interface for custom surfaces
2. Extension points for patterns
3. Custom font loaders

## Success Metrics

1. **Code Quality**
   - Test coverage > 80%
   - Type coverage > 95%
   - Zero mypy errors
   - Passing linting (ruff/black)

2. **Performance**
   - No regression in benchmarks
   - < 10% overhead vs raw Cairo

3. **Usability**
   - Installation success rate > 95%
   - Clear migration path from Pycairo
   - Comprehensive documentation

4. **Community**
   - Active issue tracking
   - Contribution guidelines
   - Regular releases

## Risk Mitigation

1. **Backward Compatibility**
   - Maintain import compatibility
   - Deprecation warnings for changed APIs
   - Migration guide

2. **Platform Issues**
   - Extensive CI testing
   - Platform-specific documentation
   - Fallback mechanisms

3. **Performance Regression**
   - Continuous benchmarking
   - Profile-guided optimization
   - Optional performance features

## Timeline Summary

- **Weeks 1-2**: Foundation and project structure
- **Weeks 3-4**: Testing and quality improvements
- **Week 5**: Documentation and examples
- **Week 6**: Deployment and distribution
- **Weeks 7-8**: Advanced features and optimization

## Conclusion

This plan transforms Qahirah from a functional but monolithic library into a modern, well-structured Python package that follows best practices. The improvements will make it more stable, easier to maintain, and simpler to deploy while maintaining its excellent Pythonic API design.