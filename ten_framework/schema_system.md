# TEN Framework Schema System

Below is an example of a TEN framework schema.

```json
"api": {
  "property": {  // The TEN schema for each property.
    "exampleInt8": {
      "type": "int8"
    },
    "exampleString": {
      "type": "string"
    }
  },
  "cmd_in": [  // The TEN schema for each input command.
    {
      "name": "cmd_1",  // The TEN schema for the input command 'cmd_1'.
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
          "x": {
            "type": "int8"
          },
          "y": {
            "type": "string"
          }
        }
      }
    }
  ],
  "cmd_out": [],           // The TEN schema for each output command.
  "data_in": [],           // The TEN schema for each input data.
  "data_out": [],          // The TEN schema for each output data.
  "video_frame_in": [],    // The TEN schema for each input video frame.
  "video_frame_out": [],   // The TEN schema for each output video frame.
  "audio_frame_in": [],    // The TEN schema for each input audio frame.
  "audio_frame_out": []    // The TEN schema for each output audio frame.
}
```

## Design Principles of the TEN Framework Schema System

The overall schema system design follows these principles:

1. **Object Principle**

   The TEN schema for any field must be defined as an object. For example, if you want to define the TEN schema for the `foo` field:

   ```json
   {
     "foo": { // The TEN schema content, defining the type as int8.
       "type": "int8"
     }
   }
   ```

   It should not be defined like this:

   ```json
   {
     "foo": "int8"
   }
   ```

   This is because the content of a TEN schema *must* be more than just a single field defining the type.

2. **Metadata-Only Principle**

   The TEN schema should define metadata only, not actual values. Fields with actual values do not belong in the TEN schema.

3. **Conflict Prevention Principle**

   In any JSON level that includes a TEN schema for user-defined fields, all fields at that level must be user-defined. This prevents conflicts between user-defined fields and non-user-defined fields. The only exception is the `_ten` field, which is a reserved field defined by the TEN runtime and can coexist with user-defined fields.

   Example of a JSON level with only user-defined fields (`foo`, `bar`):

   ```json
   {
     "foo": "int8",
     "bar": "string"
   }
   ```

   Example of a JSON level where `_ten` is not a user-defined field, but `foo` and `bar` are:

   ```json
   {
     "_ten": {
       "xxx": {}
     },
     "foo": "int8",
     "bar": "string"
   }
   ```

## Defining Types in a TEN Schema

### Primitive Types

The TEN framework supports the following types:

- int8
- int16
- int32
- int64
- uint8
- uint16
- uint32
- uint64
- float32
- float64
- string
- bool
- buf
- ptr

To define a type in a TEN schema, use the following format:

```json
"foo": {
  "type": "string"
}
```

```json
"foo": {
  "type": "int8"
}
```

```json
"foo": {
  "type": "buf"
}
```

### Complex Type: Object

```json
"foo": {
  "type": "object",
  "properties": {
    "foo": {
      "type": "int8"
    },
    "bar": {
      "type": "string"
    }
  }
}
```

### Complex Type: Array

```json
"foo": {
  "type": "array",
  "items": {
    "type": "string"
  }
}
```

## Defining the TEN Schema for Properties

Here is an example of a `property.json` file:

```json
{
  "exampleInt8": 10,
  "exampleString": "This is a test string.",
  "exampleArray": [0, 7],
  "exampleObject": {
    "foo": 100,
    "bar": "fine"
  }
}
```

The corresponding TEN schema for the above properties, as defined under the `api` field in `manifest.json`, is as follows:

> ⚠️ **Note:**
> The following definitions adhere to the Object Principle, Metadata-Only Principle, and Conflict Prevention Principle of the TEN schema.

```json
{
  "api": {
    "property": {
      "exampleInt8": {
        "type": "int8"
      },
      "exampleString": {
        "type": "string"
      },
      "exampleArray": {
        "type": "array",
        "items": {
          "type": "int64"
        }
      },
      "exampleObject": {
        "type": "object",
        "properties": {
          "foo": {
            "type": "int32"
          },
          "bar": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

## Defining the TEN Schema for Input Commands

Below is a JSON representation of an input command:

```json
{
  "_ten": {
    "name": "cmd_foo",
    "seq_id": "123",
    "dest": [{
      "app": "msgpack://127.0.0.1:8001/",
      "graph": "0",
      "extension_group": "group_a",
      "extension": "extension_b"
    }]
  },
  "foo": 3,
  "bar": "hello world"
}
```

The `_ten` field is reserved for the TEN runtime to process command information. Fields outside of `_ten`, such as `foo` and `bar` in the example, contain the actual command data.

The corresponding TEN schema for this command is:

```json
{
  "api": {
    "cmd_in": [
      {
        "name": "cmd_foo",
        "_ten": {
          "name": {
            "type": "string"
          },
          "seq_id": {
            "type": "string"
          },
          "dest": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "app": {
                  "type": "string"
                },
                "graph": {
                  "type": "string"
                },
                "extension_group": {
                  "type": "string"
                },
                "extension": {
                  "type": "string"
                }
              }
            }
          }
        },
        "property": {
          "foo": {
            "type": "int8"
          },
          "bar": {
            "type": "string"
          }
        }
      }
    ]
  }
}
```

To avoid redundancy, since the `_ten` field is reserved by the TEN runtime and its content is defined by the runtime, developers do not need to provide a TEN schema for the `_ten` field in `manifest.json`. Therefore, the TEN schema for the command in `manifest.json` should look like this:

```json
{
  "api": {
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
        }
      }
    ]
  }
}
```

> ⚠️ **Note:**
> The TEN schema within the `_ten` field cannot be modified. If a developer attempts to define it, the TEN framework will throw an error.

Each command described in `cmd_in` can have a corresponding `result` TEN schema. This `result` schema has the same structure as the command's schema and is used to describe the expected result schema for the command.

> ⚠️ **Note:**
> For input commands, the `result` schema describes the result that the extension will return after receiving the command.

```json
{
  "api": {
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
            "aaa": {
              "type": "int8"
            },
            "bbb": {
              "type": "string"
            }
          }
        }
      }
    ]
  }
}
```

## Defining the TEN Schema for Output Commands

The format is identical to that of input commands, except it is defined under `cmd_out`.

> ⚠️ **Note:**
> For output commands, the `result` schema describes the expected result that the extension will receive after sending the command.

```json
{
  "api": {
    "cmd_out": [
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
          "

property": {
            "aaa": {
              "type": "int8"
            },
            "bbb": {
              "type": "string"
            }
          }
        }
      }
    ]
  }
}
```

### Defining the TEN Schema for Input Data/VideoFrame/AudioFrame

The process is identical to defining the schema for commands, but without the `result` field.

### Defining the TEN Schema for Output Data/VideoFrame/AudioFrame

The process is identical to defining the schema for commands, but without the `result` field.
