# Metadata System

All types of TEN packages share a consistent metadata system. The TEN packages referred to here include:

- App
- Extension Group
- Extension
- Protocol
- System

In the TEN metadata system, there are two types of metadata:

- **Manifest**

  Stored in the root directory of the associated TEN package, the manifest file is named `manifest.json`.

  The manifest contains the following fields:

  - The name of the TEN package.
  - The version of the TEN package (following semantic versioning).
  - TEN schemas related to the TEN package, which include:
    - The TEN schema for the package's properties.

      These properties are typically stored in a `property.json` file located in the root directory of the package. The manifest can define the TEN schema for these properties.

    - The TEN schema for the package's input/output messages.

  > ⚠️ **Note:**
  > The TEN schema in `manifest.json` is *not* a JSON schema. JSON is not the core data type within the TEN runtime but merely a representation format. The TEN schema in `manifest.json` describes the metadata of TEN values, not JSON metadata, so it is not a JSON schema.

- **Property**

  Typically stored in a file named `property.json` in the root directory of the associated TEN package. The properties are read-write during runtime, meaning they can be modified while the TEN runtime is executing. The `property.json` file stores the initial values of these properties. For example, the following `property.json` content represents a property named "Darth Vader" with the value "I am your father."

  ```json
  {
    "Darth Vader": "I am your father"
  }
  ```

## Property

The `property.json` file defines the properties of a TEN package. An example content of a `property.json` file is as follows:

```json
{
  "prop_1_name": 0,
  "prop_2_name": "prop_2_value",
  "prop_3_name": [
    "hello",
    "prop_3_sub_value"
  ],
  "prop_4_name": {
    "prop_4_1_name": 1,
    "prop_4_2_name": "prop_4_sub_value"
  }
}
```

> ⚠️ **Note:**
> Each property name in the `property.json` file must be unique.

You can define the TEN schema for properties in the `manifest.json`, which allows the TEN runtime to handle these properties more effectively. However, it is not mandatory to define the TEN schema for every property in the `manifest.json`. If a property does not have a corresponding TEN schema, the TEN runtime will handle it using the default JSON handling method, such as treating all JSON numbers as `float64`. Conversely, if a property has a corresponding TEN schema, the TEN runtime will process it according to the information defined in the TEN schema.

> ⚠️ **Note:**
> The TEN schema for properties must comply with the TEN schema specifications of the TEN runtime.

| Property | TEN Schema | Effect |
|----------|------------|--------|
| Yes      | Yes        | The TEN runtime uses the TEN schema information to validate the property value (e.g., type). |
| Yes      | No         | The TEN runtime uses the built-in default method to handle the property value, such as treating all JSON numbers as `float64`. |

### Accessing Properties from an Extension

The TEN runtime provides a series of APIs that allow TEN extensions to access various properties.

### Specifying Property Values in the `start_graph` Command

The `start_graph` command can carry property information related to the associated TEN package. When the TEN runtime processes the `start_graph` command, it stores the property information within the corresponding TEN package instance.

As with other TEN package properties, if a TEN schema is defined in the manifest, the TEN runtime will process these properties according to the TEN schema.

The method for specifying property information in the `start_graph` command is the same as that for specifying property information in a predefined graph.

```json
{
  "nodes": [{
    "type": "extension_group",
    "name": "foo_extension_group",
    "addon": "foo_extension_group_addon"
  },{
    "type": "extension",
    "name": "bar_extension",
    "extension_group": "foo_extension_group",
    "property": {
      "prop_1_name": 0,
      "prop_2_name": "prop_2_value",
      "prop_3_name": [
        "hello",
        "prop_3_sub_value"
      ],
      "prop_4_name": {
        "prop_4_1_name": 1,
        "prop_4_2_name": "prop_4_sub_value"
      }
    }
  }]
}
```

## Manifest

Below is an example of a `manifest.json` file:

```json
{
  "type": "app",
  "name": "default_app_cpp",
  "version": "1.0.0",
  "dependencies": [
    {
      "type": "system",
      "name": "ten_runtime",
      "version": "1.0.0"
    }
  ],
  "api": {
    "property": {
      "exampleInt8": {
        "type": "int8"
      },
      "exampleString": {
        "type": "string"
      }
    },
    "cmd_in": [
      {
        "name": "cmd_foo",
        "property": {
          "foo": {
            "type": "int8"
          },
          "bar": {
            "type": "string"
          }
        },
        "result": {
          "property": {
            "detail": {
              "type": "string"
            },
            "aaa": {
              "type": "int8"
            },
            "bbb": {
              "type": "string"
            }
          }
        }
      }
    ],
    "cmd_out": [],
    "data_in": [
      {
        "name": "data_foo",
        "property": {
          "foo": {
            "type": "int8"
          },
          "bar": {
            "type": "string"
          }
        }
      }
    ],
    "data_out": [],
    "video_frame_in": [],
    "video_frame_out": [],
    "audio_frame_in": [],
    "audio_frame_out": []
  }
}
```

The primary purpose of the TEN schema in `manifest.json` is to provide the TEN runtime with metadata about the extension’s external API (e.g., type information).

> ⚠️ **Note:**
> TEN schemas are optional. Developers may choose not to specify a TEN schema, in which case the TEN runtime will use the default JSON handling method, treating all JSON numbers as `float64`.

The TEN framework uses the TEN schema in the following scenarios for more appropriate handling:

1. When the TEN runtime gets/sets properties of a TEN package or properties of a message.
2. When the TEN runtime converts a JSON document to the properties of a TEN package or message (refer to the `from_json` API in the TEN framework).
   - The TEN schema determines the type of the message property.
3. When the TEN manager checks whether a message can be output from a source extension to a destination extension, according to the TEN schema.
