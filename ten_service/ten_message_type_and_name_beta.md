---
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

# 🚧 TEN Message type and name(beta)

## Message Type and Name

Below is an overview diagram of an TEN platform message.

```
┌── has response
│   └── command
│       ├── TEN platform built-in command
│       │    => message names start with `ten::`
│       └── Non-TEN platform built-in command
│            => message names do not start with `ten::`
└── no response
  ├── data
  │   ├── TEN platform built-in data
  │   │    => message names start with `ten::`
  │   └── Non-TEN platform built-in data
  │        => message names do not start with `ten::`
  ├── image_frame
  │   ├── TEN platform built-in image_frame
  │   │    => message names start with `ten::`
  │   └── Non-TEN platform built-in image_frame
  │        => message names do not start with `ten::`
  └── pcm_frame
    ├── TEN platform built-in pcm_frame
    │    => message names start with `ten::`
    └── Non-TEN platform built-in pcm_frame
       => message names do not start with `ten::`
```

### Differentiating Message Type and Message Name

When different messages have different functionalities, message types are used to differentiate them. Functionalities refer to what the TEN Platform provides for a message, and one of the functionalities is an API. For example, the TEN Platform provides an API for getting/setting image frame data. Other functionalities include whether the message has a response, etc. For instance:

* If a message has a response (referred to as a status command in the TEN Platform), there will be a message type representing this type of message.
* When a message has an image buffer (YUV422, RGB, etc.) that can be accessed, there will be a message type representing this message with the field. This message type provides the API for getting/setting image frame data.
* When a message has an audio buffer that can be accessed, a message type called pcm frame is created. This message type provides the API for getting/setting pcm frame data.

Message names are used to differentiate the different purposes of messages within the same message type.

### Message Type

The TEN Platform messages have four types:

1. command
2. data
3. image frame
4. pcm frame

The difference between commands and non-commands is that commands have a response (referred to as a status command in the TEN Platform), while non-commands do not have a response.

The corresponding extension message callbacks are:

1. OnCmd
2. OnData
3. OnImageFrame
4. OnPcmFrame

Although there are currently four message types, it is not certain if there will only be these four types in the future. There may be new types added. If we consider this, merging the four extension message callbacks into one OnMsg can avoid the issue of adding a new extension message callback for each new message type. However, this may inconvenience users, as it means they would have to handle different message types within the OnMsg function (pseudo code).

> ```c++
> OnMsg(msg) {
>   switch (msg->type) {
>   case command || connect || timer || ...:
>     OnCmd(msg);
>     break;
>   case data:
>     OnData(msg);
>     break;
>   case image_frame:
>     OnImageFrame(msg);
>     break;
>   case pcm_frame:
>     OnPcmFrame(msg);
>     break;
>   }
> }
> ```

Additionally, in the TEN graph, there is a distinction between cmd\_in, cmd\_out, data\_in, data\_out, etc. So, following a unified approach, the interface of the extension should still maintain the differentiation of different message types.

Note

If no message type is specified, the default type is cmd.

### Message Name

Message Name is used within the TEN runtime to differentiate messages with different purposes under the same message type. The extension determines what actions to take based on the differentiation of message names.

The naming convention for message names is as follows:

1. The first character can only be a-z, A-Z, or \_.
2. Other characters can be a-z, A-Z, 0-9, or \_.

### Creating Messages

Non-TEN platform built-in command:

```json
{
  "ten": {
    "type": "cmd",           // mandatory
    "name": "hello_world"    // mandatory
  }
}
```

TEN platform built-in command:

```json
{
  "ten": {
    "type": "cmd",           // mandatory
    "name": "ten::connect"   // mandatory
  }
}
```

Data:

```json
{
  "ten": {
    "type": "data",          // mandatory
    "name": "foo"            // optional
  }
}
```

Image Frame:

```json
{
  "ten": {
    "type": "image_frame",   // mandatory
    "name": "foo"            // optional
  }
}
```

PCM Frame:

```json
{
  "ten": {
    "type": "pcm_frame",    // mandatory
    "name": "foo"           // optional
  }
}
```

### An Optimization in TEN Runtime for Message Names

Within the TEN runtime, each message is recorded with two fields:

1. Message type

    Enum.
2. Message name

    String.

Since message names are in string format, there is a certain performance overhead when comparing strings. Therefore, when the TEN runtime encounters certain specific message names, it can use a message type to represent that message. For example, when it sees a message name like ten::connect, the TEN runtime can optimize it from:

* Message type: cmd // mandatory
* Message name: ten::connect // mandatory

to:

* Message type: connect // mandatory
* Message name: ten::connect // optional

This optimization can only be applied to message names recognized by the TEN platform, so it is only possible for TEN platform built-in message names. Users cannot perform this optimization themselves, and new message types cannot be added by users. Since built-in messages are limited, the newly added message types are also limited, aligning with the enum type of message types.

In summary, message types other than cmd, data, image\_frame, and pcm\_frame are generated through this optimization, and these additional message types correspond to a special message name, which is always an TEN built-in message.

This optimization is not only applicable to commands but also to other message types. For example:

* Message type: image\_frame // mandatory
* Message name: ten::empty\_image\_frame // mandatory

can be optimized to:

* Message type: image\_frame\_empty // mandatory
* Message name: ten::empty\_image\_frame // optional

Since the TEN platform modifies the type field of the message, users can also see and use this optimization. This is not a bad thing as users can leverage it to speed up message analysis. Therefore, users can use two methods to determine the message:

1. Using the message name

    ```c++
    if (message_name == "ten::timer")
    ```

2. Using the message type

    ```c++
    if (message_type == MSG_TYPE_TIMER)
    ```

### Creating Messages (with the mentioned optimization)

TEN platform 的 built-in command:

```json
{
  "ten": {
    "type": "cmd",          // mandatory
    "name": "ten::connect"  // mandatory
  }
}
```

Or

```json
{
  "ten": {
    "type": "connect"       // mandatory
  }
}
```

### When to Specify Message Type or Message Name

When it is not possible to determine the message type or message name in the context, it is necessary to specify the message type or message name.

For example:

* Message from JSON

    When the message type or message name is unknown, it is necessary to specify the message type and message name in the JSON.
* Command from JSON

    When it is known that the message is a command but the command name is unknown, it is necessary to specify the message name in the JSON.
* Connect command from JSON

    When it is known that the message is a start_graph command, the message name can only be ten::connect. Therefore, there is no need to specify the message type or message name in the JSON.
