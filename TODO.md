# Qahirah TODO List

This is a simplified, actionable task list for improving the Qahirah project. Tasks are organized by priority and phase.

## Phase 1: Foundation and Stability

### Project Structure
- [ ] Create new directory structure (src/qahirah/...)
- [ ] Split qahirah.py into logical modules:
  - [ ] Extract Vector, Matrix, Rect, Colour to core/
  - [ ] Extract CAIRO constants and bindings to cairo/
  - [ ] Extract Surface classes to surfaces/
  - [ ] Extract Pattern classes to patterns/
  - [ ] Extract Font classes to fonts/
  - [ ] Extract Path and Region to drawing/
  - [ ] Extract platform-specific code to utils/
- [ ] Update all imports to use new structure
- [ ] Ensure backward compatibility with old imports

### Modern Build System
- [ ] Create pyproject.toml with modern configuration
- [ ] Update setup.py for backward compatibility
- [ ] Add .gitignore with Python patterns
- [ ] Create MANIFEST.in for non-Python files
- [ ] Add development dependencies (pytest, mypy, black, ruff)

### Type Annotations
- [ ] Add type hints to Vector class
- [ ] Add type hints to Matrix class
- [ ] Add type hints to Rect class
- [ ] Add type hints to Colour class
- [ ] Add type hints to Context class
- [ ] Add type hints to all Surface classes
- [ ] Add type hints to Pattern classes
- [ ] Add type hints to Font classes
- [ ] Create py.typed marker file

## Phase 2: Testing and Quality

### Test Infrastructure
- [ ] Set up pytest configuration
- [ ] Create test directory structure
- [ ] Add pytest fixtures for common objects
- [ ] Set up coverage reporting

### Unit Tests
- [ ] Write tests for Vector operations
- [ ] Write tests for Matrix operations
- [ ] Write tests for Rect operations
- [ ] Write tests for Colour conversions
- [ ] Write tests for error handling

### Integration Tests
- [ ] Test Context drawing operations
- [ ] Test Surface creation and operations
- [ ] Test Pattern functionality
- [ ] Test Font rendering
- [ ] Test Path operations

### Error Handling
- [ ] Create custom exception hierarchy
- [ ] Add context to all error messages
- [ ] Replace generic exceptions with specific ones
- [ ] Add logging support
- [ ] Document error recovery strategies

### Thread Safety
- [ ] Document current thread safety limitations
- [ ] Add thread-local storage where needed
- [ ] Create thread-safe factory methods
- [ ] Add thread safety tests

## Phase 3: Documentation

### API Documentation
- [ ] Set up Sphinx documentation
- [ ] Write comprehensive docstrings for all classes
- [ ] Document all public methods with parameters/returns
- [ ] Add usage examples to docstrings
- [ ] Create quickstart guide
- [ ] Write migration guide from Pycairo

### Examples
- [ ] Create basic shapes example
- [ ] Create gradients and patterns example
- [ ] Create text rendering example
- [ ] Create PDF generation example
- [ ] Create SVG generation example
- [ ] Create animation example
- [ ] Create performance optimization guide

## Phase 4: Deployment

### CI/CD
- [ ] Set up GitHub Actions workflow
- [ ] Add testing matrix (multiple OS/Python versions)
- [ ] Add linting checks (black, ruff)
- [ ] Add type checking (mypy)
- [ ] Set up automatic releases
- [ ] Add security scanning

### Platform Support
- [ ] Improve library detection mechanism
- [ ] Add pkg-config support
- [ ] Create platform-specific installation guides
- [ ] Test on Windows with different Cairo installations
- [ ] Test on macOS with Homebrew Cairo
- [ ] Test on various Linux distributions

### Distribution
- [ ] Build and test wheel packages
- [ ] Submit to PyPI
- [ ] Create conda-forge recipe
- [ ] Add installation troubleshooting guide

## Phase 5: Advanced Features

### Resource Management
- [ ] Implement context managers for surfaces
- [ ] Add context managers for Cairo contexts
- [ ] Create auto-cleanup decorators
- [ ] Add explicit close() methods as alternative to __del__

### Performance
- [ ] Profile common operations
- [ ] Optimize Vector/Matrix math
- [ ] Reduce Python/C boundary crossings
- [ ] Consider Cython for critical paths
- [ ] Add performance benchmarks

### Modern Python Features
- [ ] Update minimum Python version to 3.8
- [ ] Use dataclasses where appropriate
- [ ] Add f-strings for better formatting
- [ ] Use pathlib for file operations
- [ ] Add async support for I/O operations

## Maintenance Tasks

### Code Quality
- [ ] Run black formatter on entire codebase
- [ ] Fix all ruff/flake8 warnings
- [ ] Remove dead code
- [ ] Update copyright headers
- [ ] Add pre-commit hooks

### Documentation Updates
- [ ] Update README with badges
- [ ] Add contributing guidelines
- [ ] Create security policy
- [ ] Add code of conduct
- [ ] Update all external links

### Community
- [ ] Set up issue templates
- [ ] Create pull request template
- [ ] Add GitHub discussions
- [ ] Create project roadmap
- [ ] Set up project board for tracking

## Quick Wins (Can be done immediately)

- [ ] Add GitHub Actions for basic CI
- [ ] Run black formatter on codebase
- [ ] Add basic .gitignore
- [ ] Create requirements-dev.txt
- [ ] Add simple unit tests for Vector class
- [ ] Fix the bare except clause
- [ ] Add basic type hints to Vector class
- [ ] Create GitHub issues for major tasks

## Notes

- Priority should be given to testing and type hints as they will catch issues early
- Module splitting should be done carefully to maintain backward compatibility
- Documentation can be improved incrementally
- Platform-specific issues should be addressed based on user reports