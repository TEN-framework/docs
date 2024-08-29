# Version System

The TEN framework follows the semver specification.

## Version Information of TEN Package

The version information of a TEN package is recorded in the `version` field of its own `manifest.json` file. For every TEN package, this `version` field is mandatory.

``` json
{
  "version": "1.0.0"
}
```

## Version Requirement of Dependency

> ⚠️ **Note:**
> If no version requirement is specified for a dependency, the latest version is implied.

``` text
└── ten_packages/
    ├── extension/
    ├── extension_group/
    ├── protocol/
    └── system/
```

These packages are distinguished by the value of the `type` field in their `manifest.json`.

``` json
{
  "type": "system", // "extension", "extension_group", "protocol"
}
```

In a TEN package, the version requirements for the dependent TEN packages are specified as follows.

``` json
{
  "dependencies": [
    {
      "type": "system",
      "name": "ten_runtime",
      "version": "1.0.1"
    },
    {
      "type": "system",
      "name": "ten_runtime_go",
      "version": "1.0.3"
    },
    {
      "type": "extension",
      "name": "default_http_extension_cpp",
      "version": "1.0.0"
    },
    {
      "type": "extension_group",
      "name": "default_extension_group",
      "version": "1.0.0"
    }
  ]
}
```
