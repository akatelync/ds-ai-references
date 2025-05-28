## Build System Configuration
```toml
[build-system] 
requires = ["setuptools>=45", "wheel"] 
build-backend = "setuptools.build_meta"
```

- a build system configuration defines HOW your Python project gets transformed from source code into an installable package
- it tells `pip` 