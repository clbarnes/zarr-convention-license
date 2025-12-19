# License Convention Metadata

- **UUID**: b77365e5-2b0c-4141-b917-c03b7c68e935
- **Name**: License
- **Schema URL**: "<https://raw.githubusercontent.com/clbarnes/zarr-convention-license/refs/tags/v1/schema.json>"
- **Spec URL**: "<https://github.com/clbarnes/zarr-convention-license/blob/v1/README.md>"
- **Scope**: Array, Group
- **Extension Maturity Classification**: Proposal
- **Owner**: @clbarnes

## Description

This convention defines how to communicate the license(s) of the data contained in a Zarr group or array.
The license metadata exists in a field called `license` at the root `attributes` level following the [Zarr Conventions Specification](https://github.com/zarr-conventions/zarr-conventions-spec).

Communicating the license of data is applicable across ecosystems and applications,
and should be made as low-friction as possible to facilitate re-use of FAIR data.

- Examples:
  - **RECOMMENDED**: [SPDX specifier](examples/spdx.json)
  - [URL to license text](examples/url.json)
  - [License text](examples/text.json)
  - [File containing license text](examples/file.json)
  - [Path to Zarr node with license metadata](examples/path.json)

## Motivation

- **Data sharing**: Zarr data is regularly shared as a URL to a remote store; the license should be accessible alongside the data
- **Machine-discoverability**: Increasingly, data is accessed by automated systems, which should be able to parse standard licenses in situ
- **Use case alignment**: Various wrapper formats and sub-conventions may include license information, but these may not be understood by all zarr-reading tools

## Convention Registration

The convention must be registered in `zarr_conventions`:

```json
{
  "zarr_conventions": [
    {
      "schema_url": "https://raw.githubusercontent.com/clbarnes/zarr-convention-license/refs/tags/v1/schema.json",
      "spec_url": "https://github.com/clbarnes/zarr-convention-license/blob/v1/README.md",
      "uuid": "b77365e5-2b0c-4141-b917-c03b7c68e935",
      "name": "license",
      "description": "License specifier for Zarr data"
    }
  ]
}
```

## Applicable To

This convention can be used with these parts of the Zarr hierarchy:

- [x] Group
- [x] Array

## Properties

The `license` key at the root `attributes` level MUST be an array.
The members of that array MUST be an object.
The object SHOULD only have one key, as an [externally-tagged enum representation](https://serde.rs/enum-representations.html#externally-tagged).
If more than one key is used, the license represented by the object MUST be internally consistent,
i.e. do not use the "MIT" SPDX identifier and a URL to an Apache license.

An empty `license` array represents unlicensed data which SHOULD NOT be accessed without permission.

**Convention metadata name**: `license`

| Field Name | Type                                       | Description                 |
| ---------- | ------------------------------------------ | --------------------------- |
| license    | array of [License Object](#license-object) | **REQUIRED**. License enum. |

**Example**:

```json
{
  "attributes": {
    "zarr_conventions": [{ "name": "license", "spec_url": "..." }],
    "license": [
      {
        "spdx": "CC0"
      }
    ]
  }
}
```

### License Object

At least one field MUST be used.
Only one field SHOULD be used.
The `spdx` form is RECOMMENDED.

Where multiple fields are given, implementations SHOULD prefer fields at the top of the list to fields at the bottom,
and MAY treat subsequent fields as redundant.

| Field Name | Type   | Description                                                                                          |
| ---------- | ------ | ---------------------------------------------------------------------------------------------------- |
| spdx       | string | **RECOMMENDED**. An [SPDX license identifier](https://spdx.org/licenses/).                           |
| url        | string | A URL to the full text of the license, ideally using the HTTPS scheme, preferably in plain text.     |
| text       | string | The full JSON-encoded text of the license.                                                           |
| file       | string | A relative path from this Zarr node to an object containing the text of the license.                 |
| path       | string | A relative path from this Zarr node to a Zarr node with license metadata, whose license is the same. |

Some licenses require further information for attribution purposes.
Such attribution information is out of scope for this convention.

### Additional Field Information

#### `"file"` (`license[*].file`)

Paths are relative to the Zarr node (i.e. the prefix/ "directory" containing the metadata file), using POSIX semantics.
Ancestor prefixes MAY be traversed with `..`.
The current prefix MAY be referred to as `.`.
The path MAY start with `./`.
The path MUST NOT start or end with `/`.

The file containing the license text MUST be UTF-8 encoded and SHOULD contain minimal markup.

**WARNING**: Rootward references (`..`) can fail if the store uses a root which is a descendant of the highest-level element in the path.
Implementations SHOULD treat the data as unlicensed if the reference cannot be resolved to valid license metadata.

**WARNING**: Rootward references (`..`) can return misleading information if any indirection takes place in the hierarchy (e.g. file system mounts or symlinks).

#### `"path"` (`license[*].path`)

The rules around resolving relative paths is as described in the `file` section above, with the same warnings.

When resolving a `path` reference, implementations MUST take care to raise an error on cycles
(e.g. node A's license refers to node B and node B's license refers to node A).

## Known Implementations

This section helps potential implementers assess the convention's maturity and adoption, and provides a way for the community to collaborate on future revisions.

### Libraries and Tools

<!-- - **[Library/Tool Name](https://link-to-repo)** - Brief description of the implementation
  - Language: Python/JavaScript/Rust/etc.
  - Status: Experimental/Stable/Production
  - Maintainer: @github-handle
  - Since: Version X.Y or Date -->

### Datasets Using This Convention

<!-- - **[Dataset Name](https://link-to-dataset)** - Description of the dataset and how it uses this convention -->

### Resources

_If you implement or use this convention, please add your implementation to this list by submitting a pull request._

## Acknowledgements

This template is based on the [STAC extensions template](https://github.com/stac-extensions/template/blob/main/README.md).
