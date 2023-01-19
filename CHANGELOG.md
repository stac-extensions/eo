# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `solar_illumination` field for the solar illumination value of the band.
- `eo:snow_cover` field for the snow and ice cover value of the entire scene.
- Example for a Collection

### Changed

- Defined minimum values for `center_wavelength` and `full_width_half_max` (> 0)

### Deprecated

### Removed

### Fixed

- Updated Item example to STAC v1.0.0
- Fixed wavelength and FWHM values in the examples
- Added `description` to the JSON Schema.

## [v1.0.0] - 2021-03-30

Initial independent release, see [previous history](https://github.com/radiantearth/stac-spec/commits/v1.0.0-rc.2/extensions/eo)

### Fixed

- `common_name` is validated as an enum (no other values allowed)

[Unreleased]: <https://github.com/stac-extensions/eo/compare/v1.0.0...HEAD>
[v1.0.0]: <https://github.com/stac-extensions/eo/tree/v1.0.0>
