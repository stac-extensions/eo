# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Added new common names for `green05`, `rededge71`, `rededge75`, and `rededge78`
- More satellite mappings for the common names
- `eo:cloud_cover` and `eo:snow_cover` can be used in bands
- `eo:common_name`, `eo:center_wavelength`, `eo:full_width_half_max` and `eo:solar_illumination`
  can be used in Assets and Item Properties

### Changed

- `eo:bands` is now using the more general `bands` construct from STAC common metadata
- The following fields in the Band Object have been moved/renamed:
  - `name` and `description` were *not* renamed, but have been moved to STAC common metadata
  - `common_name` has been renamed to `eo:common_name`
  - `center_wavelength` has been renamed to `eo:center_wavelength`
  - `full_width_half_max` has been renamed to `eo:full_width_half_max`
  - `solar_illumination` has been renamed to `eo:solar_illumination`
- The bands in Item Properties are not meant to be the union of the asset bands any longer
  You usually don't list bands in Item Properties any longer unless all assets have the same bands.
- Small changes to the band ranges in common names to be closer to the [Awesome Spectral Indices](https://github.com/awesome-spectral-indices/awesome-spectral-indices)

### Removed

- `eo:bands` - use `bands` instead

### Fixed

- Clarified how the coverages `eo:cloud_cover` and `eo:snow_cover` should be computed

## [v1.1.0] - 2023-02-10

### Added

- `solar_illumination` field for the solar illumination value of the band.
- `eo:snow_cover` field for the snow and ice cover value of the entire scene.
- Example for a Collection

### Changed

- Defined minimum values for `center_wavelength` and `full_width_half_max` (> 0)

### Fixed

- Updated Item example to STAC v1.0.0
- Fixed wavelength and FWHM values in the examples
- Added `description` to the JSON Schema.

## [v1.0.0] - 2021-03-30

Initial independent release, see [previous history](https://github.com/radiantearth/stac-spec/commits/v1.0.0-rc.2/extensions/eo)

### Fixed

- `common_name` is validated as an enum (no other values allowed)

[Unreleased]: <https://github.com/stac-extensions/eo/compare/v1.1.0...HEAD>
[v1.1.0]: <https://github.com/stac-extensions/eo/compare/v1.0.0...v1.1.0>
[v1.0.0]: <https://github.com/stac-extensions/eo/tree/v1.0.0>
