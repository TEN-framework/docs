---
hidden: true
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🚧 How to build extension with Go(beta)

## Overview

Introduction to developing a TEN extension using the GO language, as well as debugging and deploying it to run in an app.&#x20;

This tutorial includes the following:

* How to create a GO extension development project using `arpm`.
* How to use TEN API to implement the functionality of the extension, such as sending and receiving messages.
* How to configure `cgo` if needed.
* How to write unit tests and debug code.
* How to deploy the extension locally in an app for integration testing.
* How to debug extension code in an app.

{% hint style="info" %}
Unless otherwise specified, the commands and code in this tutorial are executed in a Linux environment. Similar steps can be followed for other platforms.
{% endhint %}



## Prerequisites

Download the latest version of arpm and configure the PATH. You can check if it is configured correctly by running the following command:

```bash
$ arpm -h
```

If the configuration is correct, it will display the help information for arpm.

* Install GO 1.20 or above, preferably the latest version.

Note:

* TEN GO API uses cgo, so make sure cgo is enabled by default. You can check by running the following command:

```
$ go env CGO_ENABLED
```

If it returns 1, it means cgo is enabled by default. Otherwise, you can enable cgo by running the following command:

```
$ go env -w CGO_ENABLED=1
```

### Creating a GO Extension Project



A GO extension is essentially a go module project that includes the necessary dependencies and configuration files to meet the requirements of a TEN extension. TEN provides a default GO extension template project that developers can use to quickly create a GO extension project.

#### Creating from Template



To create a project named "first\_go\_extension" based on the default\_extension\_go template, use the following command:

```
$ arpm install extension default_extension_go --template-mode --template-data package_name=first_go_extension
```

After executing the command, a directory named "first\_go\_extension" will be created in the current directory. This directory will contain the GO extension project with the following structure:

```
.
├── default_extension.go
├── go.mod
├── manifest.json
└── property.json
```

In this structure:

* "default\_extension.go" contains a simple extension implementation that includes calls to the TEN GO API. The usage of the TEN API will be explained in the next section.
* "manifest.json" and "property.json" are the standard configuration files for TEN extensions. "manifest.json" is used to declare metadata information such as the version, dependencies, and schema definition of the extension. "property.json" is used to declare the business configuration of the extension.

The `property.json` file is initially empty and The `manifest.json` file includes a dependency on "rte\_runtime\_go" by default:

```
{
  "type": "extension",
  "name": "first_go_extension",
  "version": "0.1.0",
  "language": "go",
  "dependencies": [
    {
      "type": "system",
      "name": "rte_runtime_go",
      "version": "0.2.0"
    }
  ],
  "api": {}
}
```

Note

* Please note that according to TEN's naming convention, the name should be alphanumeric because when integrating the extension into an app, a directory will be created based on the extension name. Additionally, TEN will provide functionality to load the manifest.json and property.json files from the extension directory.
* The dependencies section declares the dependencies of the current extension. When installing the TEN package, arpm will automatically download the declared dependencies.
* The api section is used to declare the schema of the extension. For the usage of schema, refer to: [rte-schema](https://github.com/rte-design/ASTRA.ai/blob/main/docs/rte-schema.md)

TEN GO API is not publicly available and needs to be installed locally using arpm. Therefore, the go.mod file uses the replace directive to reference the TEN GO API. For example:

```
replace agora.io/rte => ../../../interface
```

When the extension is installed in an app, it will be placed in the addon/extension/ directory. At the same time, the TEN GO API will be installed in the root directory of the app. The expected directory structure is as follows:

```
.
├── addon
│   └── extension
│       └── first_go_extension
│           ├── default_extension.go
│           ├── go.mod
│           ├── manifest.json
│           └── property.json
├── go.mod
├── go.sum
├── interface
│   └── rtego
└── main.go
```

Therefore, the replace directive in the extension's go.mod file points to the interface directory in the app.

#### Manual Creation



Alternatively, developers can create a go module project using the go init command and then create the manifest.json and property.json files based on the examples provided above.

Do not add the dependency on the TEN GO API yet because the required interface directory is not available locally. It needs to be installed using arpm before adding the dependency.

To convert a newly created go module project or an existing one into an extension project, follow these steps:

* Create the property.json file in the project directory and add the necessary configuration for the extension.
* Create the manifest.json file in the project directory and specify the type, name, version, language, and dependencies information. Note that these fields are required.
  * The type should be extension.
  * The language should be go.
  * The dependencies should include the dependency on rte\_runtime\_go and any other dependencies as needed.

### Download Dependencies



Execute the following command in the extension project directory to download dependencies:

```
$ arpm install
```

After the command is successfully executed, a .rte directory will be generated in the current directory, which contains all the dependencies of the current extension.

Note

* There are two modes for an extension: development mode and runtime mode. In development mode, the root directory is the source code directory of the extension. In runtime mode, the root directory is the app directory. Therefore, the placement path of dependencies is different in these two modes. The .rte directory mentioned here is the root directory of dependencies in development mode.

The directory structure is as follows:

```
├── default_extension.go
├── go.mod
├── manifest.json
├── property.json
└── .rte
  └── app

```

In this structure, .rte/app/interface is the module for the TEN GO API.

Therefore, in development mode, the go.mod file of the extension should be modified as follows:

```
replace agora.io/rte => ./.rte/app/interface
```

If you manually created the extension as mentioned in the previous section, you also need to execute the following command in the extension directory:

```
$ go get agora.io/rte
```

The expected output should be:

```
go: added agora.io/rte v0.0.0-00010101000000-000000000000
```

At this point, a TEN GO extension project has been created.

### Implementing Extension Functionality



The "go.mod" file uses the "replace" directive to reference the TEN GO API:

```
replace agora.io/rte => ../../../interface
```

When the extension is installed in an app, it will be placed in the "addon/extension/" directory. The TEN GO API will be installed in the root directory of the app. The expected directory structure is as follows:

```
.
├── addon
│   └── extension
│       └── first_go_extension
│           ├── default_extension.go
│           ├── go.mod
│           ├── manifest.json
│           └── property.json
├── go.mod
├── go.sum
├── interface
│   └── rtego
└── main.go
```

Therefore, the "replace" directive in the extension's go.mod file points to the "interface" directory in the app.

#### Manual Creation



Alternatively, developers can manually create a go module project and then add the "manifest.json" and "property.json" files based on the examples provided above.

For a newly created go module project or an existing one, to convert it into an extension project, follow these steps:

* Create the "property.json" file in the project directory and add the necessary configuration for the extension.
* Create the "manifest.json" file in the project directory and specify the "type", "name", "version", "language", and "dependencies" information. Note that these fields are required.
  * The "type" should be "extension".
  * The "language" should be "go".
  * The "dependencies" should include the dependency on "rte\_runtime\_go" and any other dependencies as needed.

### Download Dependencies



Execute the following command in the extension project directory to download dependencies:

```
$ arpm install
```

After the command is successfully executed, a `.rte` directory will be generated in the current directory, which contains all the dependencies of the current extension.

Note:

* There are two modes for an extension: development mode and runtime mode. In development mode, the root directory is the source code directory of the extension. In runtime mode, the root directory is the app directory. Therefore, the placement path of dependencies is different in these two modes. The `.rte` directory mentioned here is the root directory of dependencies in development mode.

The directory structure is as follows:

```
├── default_extension.go
├── go.mod
├── manifest.json
├── property.json
└── .rte
  └── app
    ├── addon
    ├── include
    ├── interface
    └── lib
```

In this structure, `.rte/app/interface` is the module for the TEN GO API.

Therefore, in development mode, the `go.mod` file of the extension should be modified as follows:

```
replace agora.io/rte => ./.rte/app/interface
```

If you manually created the extension as mentioned in the previous section, you also need to execute the following command in the extension directory:

```
$ go get agora.io/rte
```

The expected output should be:

```
go: added agora.io/rte v0.0.0-00010101000000-000000000000
```

At this point, a TEN GO extension project has been created.

### Implementing Extension Functionality



For developers, there are two things to do:

* Create an extension as a channel for interaction with TEN runtime.
* Register the extension as an addon (referred to as addon in TEN) to use the extension in the graph declaratively.

#### Create extension struct



The extension created by developers needs to implement the `rtego.Extension` interface, which is defined as follows:

```
type Extension interface {
  OnInit(
    rte Rte,
    manifest MetadataInfo,
    property MetadataInfo,
  )
  OnStart(rte Rte)
  OnStop(rte Rte)
  OnDeinit(rte Rte)
  OnCmd(rte Rte, cmd Cmd)
  OnData(rte Rte, data Data)
  OnImageFrame(rte Rte, imageFrame ImageFrame)
  OnPcmFrame(rte Rte, pcmFrame PcmFrame)
}
```

It includes four lifecycle functions and four message handling functions:

Lifecycle functions:

* OnInit: Used to initialize the extension instance, such as setting the extension's configuration.
* OnStart: Used to start the extension instance, such as creating connections to external services. The extension will not receive messages until it is started. In OnStart, you can use the `rte.GetProperty` related APIs to get the extension's configuration.
* OnStop: Used to stop the extension instance, such as closing connections to external services.
* OnDeinit: Used to destroy the extension instance, such as releasing memory resources.

Message handling functions:

* OnCmd/OnData/OnImageFrame/OnPcmFrame: Callback functions for receiving four types of messages. The message types in TEN can be referred to as [message-type-and-name](https://github.com/rte-design/ASTRA.ai/blob/main/docs/message-type-and-name.md)

For the implementation of the extension, you may only need to focus on a subset of message types. To facilitate implementation, TEN provides a default `DefaultExtension`. Developers have two options: either directly implement the `rtego.Extension` interface or embed `DefaultExtension` and override the necessary methods.

For example, the `default_extension_go` template uses the approach of embedding `DefaultExtension`. Here is an example:

```
type defaultExtension struct {
  rtego.DefaultExtension
}
```

#### Register extension



After defining the extension, it needs to be registered as an addon in the TEN runtime. For example, the registration code in `default_extension.go` is as follows:

```
func init() {
  // Register addon
  rtego.RegisterAddonAsExtension(
    "default_extension_go",
    rtego.NewDefaultExtensionAddon(newDefaultExtension),
  )
}
```

* `RegisterAddonAsExtension` is the method to register an addon object as an TEN extension addon. The addon types in TEN also include extension\_group and protocol, which will be covered in the subsequent integration testing section.
  *   The first parameter is the addon name, which is a unique identifier for the addon. It will be used to define the extension in the graph declaratively. For example:

      ```
      {
        "nodes": [
          {
            "type": "extension",
            "name": "extension_go",
            "addon": "default_extension_go",
            "extension_group": "default"
          }
        ]
      }
      ```

      In this example, it means using an addon named `default_extension_go` to create an extension instance named `extension_go`.
  *   The second parameter is an addon object. TEN provides a simple way to create it - `NewDefaultExtensionAddon`, which takes the constructor of the business extension as a parameter. For example:

      ```
      func newDefaultExtension(name string) rtego.Extension {
        return &defaultExtension{}
      }
      ```

Note

* It is important to note that the addon name must be unique because in the graph, the addon name is used as a unique index to find the implementation. Here, change the first parameter to `first_go_extension`.

#### OnInit



Developers can set the extension's configuration in OnInit(), as shown below:

```
func (p *defaultExtension) OnInit(rte rtego.Rte, property rtego.MetadataInfo, manifest rtego.MetadataInfo) {
  property.Set(rtego.MetadataTypeJSONFileName, "customized_property.json")
  rte.OnInitDone()
}
```

* Both `property` and `manifest` can customize the configuration content using the `Set()` method. In the example, the first parameter `rtego.MetadataTypeJSONFileName` indicates that the custom property exists as a local file, and the second parameter is the file path relative to the extension directory. So in the example, when the app loads the extension, it will load `\<app\>/addon/extension/first_go_extension/customized_property.json`.
* TEN OnInit provides default configuration loading logic - if developers do not call `property.Set()`, the `property.json` in the extension directory will be loaded by default. Similarly, if `manifest.Set()` is not called, the `manifest.json` in the extension directory will be loaded by default. Also, if developers call `property.Set()`, the `property.json` will not be loaded by default.
* OnInit is an asynchronous method, and developers need to call `rte.OnInitDone()` to inform the TEN runtime that the initialization is expected to be completed.

Note

* Please note that `OnInitDone()` is also an asynchronous method, which means that even after `OnInitDone()` returns, developers still cannot use `rte.GetProperty()` to get the configuration. For the extension, you need to wait until the `OnStart()` callback method.

#### OnStart



When OnStart is called, it indicates that OnInitDone() has already been executed and the extension's property has been loaded. From this point on, the extension can access the configuration. Here is an example:

```
func (p *defaultExtension) OnStart(rte rtego.Rte) {
  prop, err := rte.GetPropertyString("some_string")

  if err != nil {
    // handle error.
  } else {
    // do something.
  }

  rte.OnStartDone()
}
```

`rte.GetPropertyString()` is used to retrieve a property of type string with the name "some\_string". If the property does not exist or the type does not match, an error will be returned. If the extension's configuration is as follows:

```
{
  "some_string": "hello world"
}
```

Then the value of `prop` will be "hello world".

Similar to OnInit, OnStart is also an asynchronous method, and developers need to call `rte.OnStartDone()` to inform the TEN runtime that the start process is expected to be completed.

For error handling, as shown in the previous example, `rte.GetPropertyString()` returns an error. For TEN API, errors are generally returned as `rtego.RteError` type. Therefore, you can handle the error as follows:

```
func (p *defaultExtension) OnStart(rte rtego.Rte) {
  prop, err := rte.GetPropertyString("some_string")

  if err != nil {
    // handle error.
    var rteErr *rtego.RteError
    if errors.As(err, &rteErr) {
      log.Printf("Failed to get property, cause: %s.\n", rteErr.ErrMsg())
    }
  } else {
    // do something.
  }

  rte.OnStartDone()
}
```

`rtego.RteError` provides the `ErrMsg()` method to retrieve the error message and the `ErrNo()` method to retrieve the error code.

TEN provides four types of messages: Cmd, Data, ImageFrame, and PcmFrame. Developers can handle these four types of messages by implementing the OnCmd, OnData, OnImageFrame, and OnPcmFrame callback methods.

Taking Cmd as an example, let's see how to receive and send messages.

Assume that the first\_go\_extension will receive a Cmd named "hello" with the following properties:

| name             | type   |
| ---------------- | ------ |
| app\_id          | string |
| client\_type     | int8   |
| payload          | object |
| payload.err\_no  | uint8  |
| payload.err\_msg | string |

The processing logic of the first\_go\_extension for the "hello" Cmd is as follows:

* If the app\_id or client\_type parameters are invalid, return an error:

```
{
  "err_no": 1001,
  "err_msg": "Invalid argument."
}
```

* If payload.err\_no is greater than 0, return an error with the content from the payload.
* If payload.err\_no is equal to 0, forward the "hello" Cmd downstream for further processing. After receiving the processing result from the downstream extension, return the result.

To describe the behavior of the extension in the manifest.json file, you can specify:

* What messages the extension will receive, including the message name and the structure definition of the properties (schema).
* What messages the extension will send, including the message name and the structure definition of the properties.
* For Cmd messages, a response definition is required (referred to as Result in TEN).

With these definitions, the TEN runtime will perform validation based on the schema before delivering messages to the extension and before the extension sends messages through the TEN runtime. This ensures the validity of the messages and facilitates the understanding of the extension's protocol by its users.

The schema is defined in the api field of the manifest.json file. cmd\_in defines the Cmd messages that the extension will receive, and cmd\_out defines the Cmd messages that the extension will send.

Here is an example of the manifest.json for the first\_go\_extension:

```
{
  "type": "extension",
  "name": "first_go_extension",
  "version": "0.1.0",
  "language": "go",
  "dependencies": [
    {
      "type": "system",
      "name": "rte_runtime_go",
      "version": "0.2.0"
    }
  ],
  "api": {
    "cmd_in": [
      {
        "name": "hello",
        "property": {
          "app_id": {
            "type": "string"
          },
          "client_type": {
            "type": "int8"
          },
          "payload": {
            "type": "object",
            "properties": {
              "err_no": {
                "type": "uint8"
              },
              "err_msg": {
                "type": "string"
              }
            }
          }
        },
        "required": ["app_id", "client_type"],
        "result": {
          "property": {
            "err_no": {
              "type": "uint8"
            },
            "err_msg": {
              "type": "string"
            }
          },
          "required": ["err_no"]
        }
      }
    ],
    "cmd_out": [
      {
        "name": "hello",
        "property": {
          "app_id": {
            "type": "string"
          },
          "client_type": {
            "type": "string"
          },
          "payload": {
            "type": "object",
            "properties": {
              "err_no": {
                "type": "uint8"
              },
              "err_msg": {
                "type": "string"
              }
            }
          }
        },
        "required": ["app_id", "client_type"],
        "result": {
          "property": {
            "err_no": {
              "type": "uint8"
            },
            "err_msg": {
              "type": "string"
            }
          },
          "required": ["err_no"]
        }
      }
    ]
  }
}
```

**Getting Request Data**



In the `OnCmd` method, the first step is to retrieve the request data, which is the property of the Cmd object. We define a `Request` struct to represent the request data as follows:

```
type RequestPayload struct {
  ErrNo  uint8  `json:"err_no"`
  ErrMsg string `json:"err_msg"`
}

type Request struct {
  AppID      string         `json:"app_id"`
  ClientType int8           `json:"client_type"`
  Payload    RequestPayload `json:"payload"`
}
```

For TEN message objects like Cmd, Data, PcmFrame, and ImageFrame, properties can be set. TEN provides getter/setter APIs for properties. The logic to retrieve the request data is to use the `GetProperty` API to parse the property of the Cmd object, as shown below:

```
func parseRequestFromCmdProperty(cmd rtego.Cmd) (*Request, error) {
  request := &Request{}

  if appID, err := cmd.GetPropertyString("app_id"); err != nil {
    return nil, err
  } else {
    request.AppID = appID
  }

  if clientType, err := cmd.GetPropertyInt8("client_type"); err != nil {
    return nil, err
  } else {
    request.ClientType = clientType
  }

  if payloadBytes, err := cmd.GetPropertyToJSONBytes("payload"); err != nil {
    return nil, err
  } else {
    err := json.Unmarshal(payloadBytes, &request.Payload)

    rtego.ReleaseBytes(payloadBytes)

    if err != nil {
      return nil, err
    }
  }

  return request, nil
}
```

* `GetPropertyString()` and `GetPropertyInt8()` are specialized APIs to retrieve properties of specific types. For example, `GetPropertyString()` expects the property to be of type string, and if it's not, an error will be returned.
* `GetPropertyToJSONBytes()` expects the value of the property to be a JSON-serialized data. This API is provided because TEN runtime does not expect to be bound to a specific JSON library. After obtaining the property as a slice, developers can choose a JSON library for deserialization as needed.
* `rtego.ReleaseBytes()` is used to release the `payloadBytes` because TEN GO binding layer provides a memory pool.

**Returning a Response**



After parsing the request data, we can now implement the first step of the processing flow - returning an error response if the parameters are invalid. In TEN extensions, a response is represented by a `CmdResult`. So, returning a response involves the following two steps:

* Creating a `CmdResult` object and setting properties as needed.
* Handing over the created `CmdResult` object to the TEN runtime, which will handle returning it based on the path of the requester.

Here's the implementation:

```
const InvalidArgument uint8 = 1

func (r *Request) validate() error {
  if len(r.AppID) < 64 {
    return errors.New("invalid app_id")
  }

  if r.ClientType != 1 {
    return errors.New("invalid client_type")
  }

  return nil
}

func (p *defaultExtension) OnCmd(
  rte rtego.Rte,
  cmd rtego.Cmd,
) {
  request, err := parseRequestFromCmdProperty(cmd)
  if err == nil {
    err = request.validate()
  }

  if err != nil {
    result, fatal := rtego.NewCmdResult(rtego.Error)
    if fatal != nil {
      log.Fatalf("Failed to create result, %v\n", fatal)
      return
    }

    result.SetProperty("err_no", InvalidArgument)
    result.SetPropertyString("err_msg", err.Error())

    rte.ReturnResult(result, cmd)
  }
}
```

* `rtego.NewCmdResult()` is used to create a `CmdResult` object. The first parameter is the error code - either Ok or Error. This error code is built-in to the TEN runtime and indicates whether the processing was successful. Developers can also use `GetStatusCode()` to retrieve this error code. Additionally, developers can define more detailed business-specific error codes, as shown in the example.
* `result.SetProperty()` is used to set properties in the `CmdResult` object. Properties are set as key-value pairs. `SetPropertyString()` is a specialized API of `SetProperty()` and is provided to reduce the performance overhead of passing GO strings.
* `rte.ReturnResult()` is used to return the `CmdResult` to the requester. The first parameter is the response, and the second parameter is the request. The TEN runtime will handle returning the response to the requester based on the request's path.

**Passing the Request to Downstream Extensions**



If an extension wants to send a message to another extension, it can call the `SendCmd()` API. Here's an example:

```
func (p *defaultExtension) OnCmd(
  rte rtego.Rte,
  cmd rtego.Cmd,
) {
  // ...

  if err != nil {
    // ...
  } else {
    // Dispatch the request to the upstream.
    rte.SendCmd(cmd, func(r rtego.Rte, result rtego.CmdResult) {
      r.ReturnResultDirectly(result)
    })
  }
}
```

* The first parameter in `SendCmd()` is the command of the request, and the second parameter is the callback function that handles the `CmdResult` returned by the downstream. The second parameter can also be set to `nil`, indicating that no special handling is required for the result returned by the downstream. If the original command was sent from a higher-level extension, the runtime will automatically return it to the upper-level extension.
* In the example's callback function, `ReturnResultDirectly()` is used. You can see that this method differs from `ReturnResult()` in that it does not pass the original command object. This is mainly because:
  * For TEN message objects like Cmd/Data/PcmFrame/ImageFrame, there is an ownership concept. In the extension's message callback method, such as `OnCmd()`, the TEN runtime transfers ownership of the Cmd object to the extension. This means that once the extension receives the Cmd, the TEN runtime will not perform any read or write operations on it. When the extension calls the `SendCmd()` or `ReturnResult()` API, it returns ownership of the Cmd back to the TEN runtime, which handles further processing, such as message delivery. After that, the extension should not perform any read or write operations on the Cmd.
  * The `result` in the response handler (the second parameter of `SendCmd()`) is returned by the downstream, and at this point, the result is already bound to the Cmd, meaning the runtime has information about the result's return path. Therefore, there is no need to pass the Cmd object again.

Of course, developers can also handle the result in the response handler.

So far, an example of a simple Cmd processing logic is complete. For other message types like Data, you can refer to the TEN API documentation.

### Deploying Locally to the App for Integration Testing



arpm provides the ability to publish and use a local registry, allowing you to perform integration testing locally without uploading the extension to the central repository. Here are the steps:

* Set up the arpm local registry.
* Upload the extension to the local registry.
* Download the default\_app\_go from the central repository as the integration testing environment, along with any other dependencies needed.
* Download the first\_go\_extension from the local registry.
* Configure the graph in default\_app\_go to specify the receiver of the message as first\_go\_extension and send test messages.

#### Uploading the Extension to the Local Registry



Before uploading, restore the dependency path for TEN GO binding in first\_go\_extension/go.mod because it needs to be installed under the app. For example:

```
replace agora.io/rte => ../../../interface
```

First, create a temporary config.json file to set up the arpm local registry. For example, the contents of /tmp/code/config.json are as follows:

```
{
  "registry": [
    "file:///tmp/code/repository"
  ]
}
```

This sets the local directory /tmp/code/repository as the arpm local registry.

Note

* Make sure not to place it in \~/.arpm/config.json as it will affect downloading dependencies from the central repository.

Then, in the first\_go\_extension directory, execute the following command to upload the extension to the local registry:

```
$ arpm --config-file /tmp/code/config.json publish
```

After the command completes, the uploaded extension can be found in the directory /tmp/code/repository/extension/first\_go\_extension/0.1.0.

#### Prepare the test app



1. Install `default_app_go` as the test app in an empty directory.

```
$ arpm install app default_app_go
```

After the command is successful, you will have a directory named `default_app_go` in the current directory.

> **Note**
>
> * Since the extension being tested is written in Go, the app must also be written in Go. `default_app_go` is a Go app template provided by TEN.
> * When installing the app, its dependencies will be automatically installed.

2. Install the `extension_group` in the app directory.

Switch to the app directory:

```
$ cd default_app_go
```

Install the `default_extension_group`:

```
$ arpm install extension_group default_extension_group
```

After the command completes, you will have a directory named `addon/extension_group/default_extension_group`.

> **Note**
>
> * `extension_group` is a capability provided by TEN to declare physical threads and specify which extension instances run in which extension groups. `default_extension_group` is the default extension group provided by TEN.

3. Install the `first_go_extension` that we want to test in the app directory.

Execute the following command:

```
$ arpm --config-file /tmp/code/config.json install extension first_go_extension
```

After the command completes, you will have a directory named `addon/extension/first_go_extension`.

> **Note**
>
> * It is important to note that since `first_go_extension` is in the local registry, you need to specify the configuration file path that contains the local registry configuration using `--config-file`, just like when publishing.

4. Add an extension as a message producer.

> The first\_go\_extension is expected to receive a hello cmd, so we need a message producer. One way to achieve this is by adding an extension as a message producer. To conveniently generate test messages, we can integrate an HTTP server into the producer's extension.
>
> First, we can create an HTTP server extension based on default\_extension\_go. Execute the following command in the app directory:
>
> ```
> $ arpm install extension default_extension_go --template-mode --template-data package_name=http_server
> ```
>
> Modify the module name in addon/extension/http\_server/go.mod to http\_server.
>
> The main functionalities of the HTTP server are as follows:
>
> * Start an HTTP server in the extension's OnStart() method, running as a goroutine.
> * Convert incoming requests into TEN Cmd with the name hello, and then call SendCmd() to send the message.
> * Expect to receive a CmdResult response and write its content to the HTTP response.
>
> The code implementation is as follows:
>
> ```
> type defaultExtension struct {
>   rtego.DefaultExtension
>   rte rtego.Rte
>
>   server *http.Server
> }
>
> type RequestPayload struct {
>   ErrNo  uint8  `json:"err_no"`
>   ErrMsg string `json:"err_msg"`
> }
>
> type Request struct {
>   AppID      string         `json:"app_id"`
>   ClientType int8           `json:"client_type"`
>   Payload    RequestPayload `json:"payload"`
> }
>
> func newDefaultExtension(name string) rtego.Extension {
>   return &defaultExtension{}
> }
>
> func (p *defaultExtension) defaultHandler(writer http.ResponseWriter, request *http.Request) {
>   switch request.URL.Path {
>   case "/health":
>     writer.WriteHeader(http.StatusOK)
>   default:
>     resultChan := make(chan rtego.CmdResult, 1)
>
>     var req Request
>     if err := json.NewDecoder(request.Body).Decode(&req); err != nil {
>       writer.WriteHeader(http.StatusBadRequest)
>       writer.Write([]byte("Invalid request body."))
>       return
>     }
>
>     cmd, _ := rtego.NewCmd("hello")
>     cmd.SetPropertyString("app_id", req.AppID)
>     cmd.SetProperty("client_type", req.ClientType)
>
>     payloadBytes, _ := json.Marshal(req.Payload)
>     cmd.SetPropertyFromJSONBytes("payload", payloadBytes)
>
>     p.rte.SendCmd(cmd, func(rte rtego.Rte, result rtego.CmdResult) {
>       resultChan <- result
>     })
>
>     result := <-resultChan
>
>     writer.WriteHeader(http.StatusOK)
>     errNo, _ := result.GetPropertyUint8("err_no")
>
>     if errNo > 0 {
>       errMsg, _ := result.GetPropertyString("err_msg")
>       writer.Write([]byte(errMsg))
>     } else {
>       writer.Write([]byte("OK"))
>     }
>   }
> }
>
> func (p *defaultExtension) OnStart(rte rtego.Rte) {
>   p.rte = rte
>
>   mux := http.NewServeMux()
>   mux.HandleFunc("/", p.defaultHandler)
>
>   p.server = &http.Server{
>     Addr:    ":8001",
>     Handler: mux,
>   }
>
>   go func() {
>     if err := p.server.ListenAndServe(); err != nil {
>       if err != http.ErrServerClosed {
>         panic(err)
>       }
>     }
>   }()
>
>   go func() {
>     // Check if the server is ready.
>     for {
>       resp, err := http.Get("http://127.0.0.1:8001/health")
>       if err != nil {
>         continue
>       }
>
>       defer resp.Body.Close()
>
>       if resp.StatusCode == 200 {
>         break
>       }
>
>       time.Sleep(50 * time.Millisecond)
>     }
>
>     fmt.Println("http server starts.")
>
>     p.rte.OnStartDone()
>   }()
> }
>
> func (p *defaultExtension) OnStop(rte rtego.Rte) {
>   fmt.Println("defaultExtension OnStop")
>
>   if p.server != nil {
>     p.server.Shutdown(context.Background())
>   }
>
>   rte.OnStopDone()
> }
>
> func init() {
>   fmt.Println("defaultExtension init")
>
>   // Register addon
>   rtego.RegisterAddonAsExtension(
>     "http_server",
>     rtego.NewDefaultExtensionAddon(newDefaultExtension),
>   )
> }
> ```

1. Configure the graph.

> In the app's `manifest.json`, configure `predefined_graph` to specify the `hello` cmd generated by `http_server` and send it to `first_go_extension`. For example:

```
"predefined_graphs": [
  {
    "name": "testing",
    "auto_start": true,
    "nodes": [
      {
        "type": "extension_group",
        "name": "http_thread",
        "addon": "default_extension_group"
      },
      {
        "type": "extension",
        "name": "http_server",
        "addon": "http_server",
        "extension_group": "http_thread"
      },
      {
        "type": "extension",
        "name": "first_go_extension",
        "addon": "first_go_extension",
        "extension_group": "http_thread"
      }
    ],
    "connections": [
      {
        "extension_group": "http_thread",
        "extension": "http_server",
        "cmd": [
          {
            "name": "hello",
            "dest": [
              {
                "extension_group": "http_thread",
                "extension": "first_go_extension"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

6. Compile the app and start it.

> Run the following command in the app directory:

```
$ go run scripts/build/main.go
```

After the compilation is complete, an executable file `./bin/main` will be generated by default.

Start the app by running the following command:

```
$ ./bin/main
```

When the console outputs the log message "http server starts", it means that the HTTP listening port has been successfully started. You can now send requests to test it.

For example, use curl to send a request with an invalid `app_id`:

```
$ curl --location 'http://127.0.0.1:8001/hello' \
  --header 'Content-Type: application/json' \
  --data '{
      "app_id": "123",
      "client_type": 1,
      "payload": {
          "err_no": 0
      }
  }'
```

You should expect to receive a response with the message "invalid app\_id".

### Debugging an extension in the app



The compilation of an TEN app is performed through a script defined by TEN, which includes the necessary configuration settings for compilation. In other words, you cannot compile an TEN app directly using "go build". Therefore, you cannot debug the app using the default method and instead need to choose the "attach" method.

For example, if you want to debug the app in Visual Studio Code, you can add the following configuration to ".vscode/launch.json":

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "app (golang) (go, attach)",
      "type": "go",
      "request": "attach",
      "mode": "local",
      "processId": 0,
      "stopOnEntry": true
    }
  ]
}
```

First, compile and start the app using the above method.

Then, in the "RUN AND DEBUG" window of Visual Studio Code, select "app (golang) (go, attach)" and click "Start Debugging".

Next, in the pop-up process selection window, locate the running app process and start debugging.

Note:

* If you encounter the "the scope of ptrace system call application is limited" error on a Linux environment, you can resolve it by running the following command:

```
$ sudo sysctl -w kernel.yama.ptrace_scope=0
```

\
