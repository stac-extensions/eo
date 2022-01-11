# Electro-Optical Extension Specification

- **Title:** Electro-Optical
- **Identifier:** <https://stac-extensions.github.io/eo/v1.0.0/schema.json>
- **Field Name Prefix:** eo
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Stable
- **Owner**: @matthewhanson
- **History:** [Prior to March 30, 2021](https://github.com/radiantearth/stac-spec/commits/v1.0.0-rc.2/extensions/eo)

This document explains the fields of the Electro-Optical (EO) Extension to the
[SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

EO data is considered to be data that represents a snapshot of the Earth for a single date and time. It
could consist of multiple spectral bands in any part of the electromagnetic spectrum. Examples of EO
data include sensors with visible, short-wave and mid-wave IR bands (e.g., the OLI instrument on
Landsat-8), long-wave IR bands (e.g. TIRS aboard Landsat-8).

It is strongly recommended to use [Instrument Fields](https://github.com/radiantearth/stac-spec/tree/master/item-spec/common-metadata.md#instrument) 
with the EO extension, to provide information about the platform (satellite, aerial, etc) used to capture the images.

For defining view geometry of data, it is strongly recommended to use the [`view` extension](https://github.com/stac-extensions/view).

- Examples:
  - [Item example](examples/item.json)
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Item Properties or Asset Fields

| Field Name     | Type                           | Description |
| -------------- | ------------------------------ | ----------- |
| eo:bands       | \[[Band Object](#band-object)] | An array of available bands where each object is a [Band Object](#band-object). If given, requires at least one band. |
| eo:cloud_cover | number                         | Estimate of cloud cover |

### Additional Field Information

#### eo:bands

The `eo:bands` array is used to describe the available [spectral 
bands](https://www.sciencedirect.com/topics/earth-and-planetary-sciences/spectral-band) in an Asset. This enables clients to read  
the file and understand which band is 'red' and which is 'nir' (near infrared) so that it can perform an 
[NDVI](https://en.wikipedia.org/wiki/Normalized_difference_vegetation_index) operation, for example. Each Asset should specify
its own band object. If the individual bands are repeated in different assets they should all use the same values and 
include the optional [`name`](#name) field to enable clients to easily combine and summarize the bands.

The `eo:bands` array may optionally be used in the Item Properties to summarize the available bands in the assets. This should
be a 'union' of all the possible bands represented in assets. It should be considered merely informative - clients should rely
on the `eo:bands` of each asset. An Item is only allowed to use `eo:bands` in its Properties if it has at least one asset with 
a defined bands array.

**NOTE**: In STAC versions 0.9.x and prior, `eo:bands` could only be used by an Asset putting the the Band Object definitions in 
an Item Properties and referencing these via array index. 1.0.0-beta.1 introduced the current behavior.

#### eo:cloud_cover

Estimate of cloud cover as a percentage (0-100) of the entire scene. If not available, the field should not be provided. Generally, 
this value should be used in Item Properties rather than Item Assets, as an Item from an electro-optical source is a single snapshot 
of the Earth, so the cloud cover value would apply to all assets. 

#### eo:snow_cover

Estimate of snow or ice covered surfaces as a percentage (0-100) of the entire scene. If not available, the field should not be provided. Generally, 
this value should be used in Item Properties rather than Item Assets, as an Item from an electro-optical source is a single snapshot 
of the Earth, so the snow cover value would apply to all assets. 
### Band Object

| Field Name          | Type   | Description |
| ------------------- | ------ | ----------- |
| name                | string | The name of the band (e.g., "B01", "B8", "band2", "red"). |
| common_name         | string | The name commonly used to refer to the band to make it easier to search for bands across instruments. See the [list of accepted common names](#common-band-names). |
| description         | string | Description to fully explain the band. [CommonMark 0.29](http://commonmark.org/) syntax MAY be used for rich text representation. |
| center_wavelength   | number | The center wavelength of the band, in micrometers (μm). |
| full_width_half_max | number | Full width at half maximum (FWHM). The width of the band, as measured at half the maximum transmission, in micrometers (μm). |
| solar_illumination  | number | The solar illumination of the band, as measured at half the maximum transmission, in W/m2/micrometers. |

*At least one of the fields must be specified.*

#### name

This is typically the name the data provider uses for the band. It should be treated like an 'id', to identify that a particular
band used in several assets represents the same band (all the other fields should be the same as well). It is also recommended that
clients use this name for display, potentially in conjunction with the common name.

#### center_wavelength and full_width_half_max

These fields are a common way to approximately describe a spectral band. In most cases even these numbers are not as useful as the 
`common_name` that should be supplied with the spectral bands, where they exist. For non-standard bands (such as with hyperspectral sensors) 
the wavelength fields indicate where the band is.

Another common way to define a spectral band with a minimum and maximum wavelength, where outside these bounds the transmission is 0%, 
and non-zero inside the bounds (e.g., 80%). The maximum transmission of a band is not captured in any of these metrics, 
nor is it important in most cases.

However, spectral transmission for a filter does not go from 0% to a constant max value (e.g., 80%) then back to 0%. Such a filter is 
referred to as a "top-hat" filter due to it's shape, but does not exist in reality. Thus, the minimum and maximum wavelengths are 
typically selected to be the point at which transmission drops below some threshold, and this threshold is often half of the maximum 
transmission. Thus if a filter's maximum transmission is 80%, the min and max thresholds would be the points where the transmission drops below 40%.

The `center_wavelength` of a band is the midpoint between the min and max wavelengths:

```python
center_wavelength = (min_wavelength + max_wavelength) / 2
```

The `full_width_half_max` (FWHM) is the difference between the min and max wavelengths, 
thus representing the width of the band at half it's maximum transmission.

```python
full_width_half_max = max_wavelength - min_wavelength
```

For example, if we were given a band described as (0.4um - 0.5um) the `center_wavelength` would be 0.45um 
and the `full_width_half_max` would be 0.1um.

In some cases the full transmission profile is needed, such as when harmonizing between two sensor modalities. It is recommended
 that the full spectral profile be included as a link or an asset (preferably at the 
 [Collection](https://github.com/radiantearth/stac-spec/tree/master/collection-spec/collection-spec.md) level).

#### solar_illumination

In satellite-based remote sensing applications, the calibration of the sensor recorded top-of-atmosphere reflectance to radiance, or vice-versa,
is carried out using a reference spectral solar irradiance value.
It depends of the extra-terrestrial solar irradiance (e.g. [[Thuillier et al., 2003](https://link.springer.com/article/10.1023/A:1024048429145)])
at a specific spectral wavelength,
of the illumination conditions during the calibration data acquisition (through platform navigation and attitude),
of the instrument viewing geometry on the diffuser panel.
The value in this field is the is the mean value representing the solar illumination in W/m2/micrometers
at a specific level (e.g. Top of Atmosphere) for the asset.
For instance, this value is used for optical calibration of an asset (e.g. [ESUN(b) value in OTB optical calibration application](https://www.orfeo-toolbox.org/CookBook/Applications/app_OpticalCalibration.html#description))

#### Common Band Names

The band's `common_name` is the name that is commonly used to refer to that band's spectral
properties. The table below shows the allowed common names based on the average band range for the band
numbers of several popular instruments.

| Common Name | Band Range (μm) | Landsat 5/7 | Landsat 8 | Sentinel 2 | MODIS | NAIP |
| ----------- | --------------- | ----------- | --------- | ---------- | ----- | ---- |
| coastal     | 0.40 - 0.45     |             | 1         | 1          |       |      |
| blue        | 0.45 - 0.50     | 1           | 2         | 2          | 3     | 3    |
| green       | 0.50 - 0.60     | 2           | 3         | 3          | 4     | 2    |
| red         | 0.60 - 0.70     | 3           | 4         | 4          | 1     | 1    |
| yellow      | 0.58 - 0.62     |             |           |            |       |      |
| pan         | 0.50 - 0.70     | 8 (*L7 only*) | 8       |            |       |      |
| rededge     | 0.70 - 0.79     |             |           | 5, 6, 7    |       |      |
| nir         | 0.75 - 1.00     | 4           |           | 8          | 2     | 4    |
| nir08       | 0.75 - 0.90     |             | 5         | 8a         |       |      |
| nir09       | 0.85 - 1.05     |             |           | 9          |       |      |
| cirrus      | 1.35 - 1.40     |             | 9         | 10         | 26    |      |
| swir16      | 1.55 - 1.75     | 5           | 6         | 11         | 6     |      |
| swir22      | 2.10 - 2.30     | 7           | 7         | 12         | 7     |      |
| lwir        | 10.5 - 12.5     | 6           |           |            |       |      |
| lwir11      | 10.5 - 11.5     |             | 10        |            | 31    |      |
| lwir12      | 11.5 - 12.5     |             | 11        |            | 32    |      |

The difference between the `nir`, `nir08`, and `nir09` bands are that the `nir` band is a wider band that covers 
most of the spectral range of 0.75μm to 1.0μm. `nir08` and `nir09` are narrow bands centered 0.85μm and 0.95μm 
respectively. The same goes for the difference between `lwir`, `lwir11` and `lwir12`.

## Best Practices

One of the emerging best practices is to use [Asset Roles](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#asset-roles)
to provide clients with more information about the assets in an item. The following list includes a shared vocabulary for some common EO assets. 
This list should not be considered definitive, and implementors are welcome to use other asset roles. If consensus and tooling consolidates around
these role names then they will be specified in the future as more standard than just 'best practices'.

| Role Name | Description                                                            |
| --------- | ---------------------------------------------------------------------- |
| reflectance | An asset the provides [reflectance](https://www.l3harrisgeospatial.com/Support/Self-Help-Tools/Help-Articles/Help-Articles-Detail/ArtMID/10220/ArticleID/19247/3377) values, instead of just radiance. |
| temperature | An asset that provides actual temperature measurements. |
| saturation |  Points to a file that indicates where pixels in the input spectral bands are saturated. |
| cloud | Points to a file that indicates whether a pixel is assessed as being cloud |
| cloud-shadow | Points to a file that indicates whether a pixel is assessed as being cloud shadow. |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
