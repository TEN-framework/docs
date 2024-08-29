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

# TEN API(beta)

## TEN Platform API Specification

### C Core

#### Error

> > Default errno, for those users only care error msgs.
>
> > Invalid json.
>
> > Invalid argument.
>
> > Invalid graph.
>
> > The TEN world is closed.

### C++

#### Message

| Name                              | Link                                   |
| --------------------------------- | -------------------------------------- |
| msg\_t::get\_type                 | `Link <msg_t::get_type>`               |
| cmd\_t::get\_name                 | `Link <msg_t::get_name>`               |
| msg\_t::set\_dest                 | `Link <msg_t::set_dest>`               |
| msg\_t::from\_json                | `Link <msg_t::from_json>`              |
| msg\_t::to\_json                  | `Link <msg_t::to_json>`                |
| msg\_t::is\_property\_exist       | `Link <msg_t::is_property_exist>`      |
| msg\_t::get\_property\_uint8      | `Link <msg_t::get_property_uint8>`     |
| msg\_t::get\_property\_uint16     | `Link <msg_t::get_property_uint16>`    |
| msg\_t::get\_property\_uint32     | `Link <msg_t::get_property_uint32>`    |
| msg\_t::get\_property\_uint64     | `Link <msg_t::get_property_uint64>`    |
| msg\_t::get\_property\_int8       | `Link <msg_t::get_property_int8>`      |
| msg\_t::get\_property\_int16      | `Link <msg_t::get_property_int16>`     |
| msg\_t::get\_property\_int32      | `Link <msg_t::get_property_int32>`     |
| msg\_t::get\_property\_int64      | `Link <msg_t::get_property_int64>`     |
| msg\_t::get\_property\_float32    | `Link <msg_t::get_property_float32>`   |
| msg\_t::get\_property\_float64    | `Link <msg_t::get_property_float64>`   |
| msg\_t::get\_property\_string     | `Link <msg_t::get_property_string>`    |
| msg\_t::get\_property\_bool       | `Link <msg_t::get_property_bool>`      |
| msg\_t::get\_property\_ptr        | `Link <msg_t::get_property_ptr>`       |
| msg\_t::get\_property\_buf        | `Link <msg_t::get_property_buf>`       |
| msg\_t::get\_property\_to\_json   | `Link <msg_t::get_property_to_json>`   |
| msg\_t::set\_property\_uint8      | `Link <msg_t::set_property_uint8>`     |
| msg\_t::set\_property\_uint16     | `Link <msg_t::set_property_uint16>`    |
| msg\_t::set\_property\_uint32     | `Link <msg_t::set_property_uint32>`    |
| msg\_t::set\_property\_uint64     | `Link <msg_t::set_property_uint64>`    |
| msg\_t::set\_property\_int8       | `Link <msg_t::set_property_int8>`      |
| msg\_t::set\_property\_int16      | `Link <msg_t::set_property_int16>`     |
| msg\_t::set\_property\_int32      | `Link <msg_t::set_property_int32>`     |
| msg\_t::set\_property\_int64      | `Link <msg_t::set_property_int64>`     |
| msg\_t::set\_property\_float32    | `Link <msg_t::set_property_float32>`   |
| msg\_t::set\_property\_float64    | `Link <msg_t::set_property_float64>`   |
| msg\_t::set\_property\_string     | `Link <msg_t::set_property_string>`    |
| msg\_t::set\_property\_bool       | `Link <msg_t::set_property_bool>`      |
| msg\_t::set\_property\_ptr        | `Link <msg_t::set_property_ptr>`       |
| msg\_t::set\_property\_buf        | `Link <msg_t::set_property_buf>`       |
| msg\_t::set\_property\_from\_json | `Link <msg_t::set_property_from_json>` |

C++ message API

> Get the message type.

> Get the message name.

> Set the destination of the message.

> Convert the message to JSON string.

> Convert the message from JSON string.

> Check if the property exists. path can not be empty.

> Get the property from the message in the specified type. path can not be empty.

> Get the property from the message in JSON format. path can not be empty.

> Set the property in the message from JSON string. path can not be empty.

#### Command

| Name                          | Link                             |
| ----------------------------- | -------------------------------- |
| cmd\_t::create(const char \*) | `Link <cmd_t::create>`           |
| cmd\_t::create\_from\_json    | `Link <cmd_t::create_from_json>` |

C++ command API

> Create a new command with the specified command name.

> Create a new command from a JSON string.

#### Connect Command

| Name                      | Link                           |
| ------------------------- | ------------------------------ |
| cmd\_connect\_t::create() | `Link <cmd_connect_t::create>` |

C++ connect command API

> Create a new connect command.

#### Status Command

| Name                                    | Link                                        |
| --------------------------------------- | ------------------------------------------- |
| cmd\_result\_t::create(STATUS\_CODE)    | `Link <cmd_result_t::cmd_result_t>`         |
| cmd\_result\_t::get\_detail\<T>         | `Link <cmd_result_t::get_detail>`           |
| cmd\_result\_t::get\_detail\_to\_json   | `Link <cmd_result_t::get_detail_to_json>`   |
| cmd\_result\_t::set\_detail\<T>         | `Link <cmd_result_t::set_detail>`           |
| cmd\_result\_t::set\_detail\_from\_json | `Link <cmd_result_t::set_detail_from_json>` |
| cmd\_result\_t::get\_status\_code       | `Link <cmd_result_t::get_status_code>`      |

C++ status command API

> Create a new status command with the specified status code.

> Get the detail of the status command in JSON format.

> Set the detail of the status command from JSON string.

> Get the status code from the status command.

#### Timeout Command

| Name                              | Link                                 |
| --------------------------------- | ------------------------------------ |
| cmd\_timeout\_t::create()         | `Link <cmd_timeout_t::create>`       |
| cmd\_timeout\_t::get\_timer\_id() | `Link <cmd_timeout_t::get_timer_id>` |

C++ timeout command API

> Create a new timeout command.

> Get the corresponding timer ID from the timeout command.

#### Timer Command

| Name                    | Link                         |
| ----------------------- | ---------------------------- |
| cmd\_timer\_t::create() | `Link <cmd_timer_t::create>` |

C++ timer command API

> Create a new timer command.

#### Close App Command

| Name                         | Link                             |
| ---------------------------- | -------------------------------- |
| cmd\_close\_app\_t::create() | `Link <cmd_close_app_t::create>` |

C++ close app command API

> Create a new close app command.

#### Close Engine Command

| Name                            | Link                                |
| ------------------------------- | ----------------------------------- |
| cmd\_close\_engine\_t::create() | `Link <cmd_close_engine_t::create>` |

C++ close engine command API

> Create a new close engine command.

#### Data Message

| Name                 | Link                        |
| -------------------- | --------------------------- |
| data\_t::create()    | `Link <data_t::create>`     |
| data\_t::get\_buf    | `Link <data_t::get_buf>`    |
| data\_t::lock\_buf   | `Link <data_t::lock_buf>`   |
| data\_t::unlock\_buf | `Link <data_t::unlock_buf>` |

C++ data message API

> Create a data message.

> Get the buffer of the data message. The operation is deep-copy.

> Borrow the ownership of the buffer from the data message.

> Give the ownership of the buffer back to the data message.

#### Image frame Message

| Name                             | Link                                  |
| -------------------------------- | ------------------------------------- |
| image\_frame\_t::create()        | `Link <image_frame_t::create>`        |
| image\_frame\_t::get\_width      | `Link <image_frame_t::get_width>`     |
| image\_frame\_t::set\_width      | `Link <image_frame_t::set_width>`     |
| image\_frame\_t::get\_height     | `Link <image_frame_t::get_height>`    |
| image\_frame\_t::set\_height     | `Link <image_frame_t::set_height>`    |
| image\_frame\_t::get\_timestamp  | `Link <image_frame_t::get_timestamp>` |
| image\_frame\_t::set\_timestamp  | `Link <image_frame_t::set_timestamp>` |
| image\_frame\_t::get\_pixel\_fmt | `Link <image_frame_t::get_pixel_fmt>` |
| image\_frame\_t::set\_pixel\_fmt | `Link <image_frame_t::set_pixel_fmt>` |
| image\_frame\_t::is\_eof         | `Link <image_frame_t::is_eof>`        |
| image\_frame\_t::set\_is\_eof    | `Link <image_frame_t::set_is_eof>`    |
| image\_frame\_t::alloc\_buf      | `Link <image_frame_t::alloc_buf>`     |
| image\_frame\_t::lock\_buf       | `Link <image_frame_t::lock_buf>`      |
| image\_frame\_t::unlock\_buf     | `Link <image_frame_t::unlock_buf>`    |

C++ image frame message API

> Create a image frame message.

> Get/set the width of the image frame.

> Get/set the height of the image frame.

> Get/set the timestamp of the image frame.

> Get/set the pixel format type of the image frame.

> Get/set the end of file flag of the image frame.

> Allocate a buffer for the image frame.

> Borrow the ownership of the buffer from the image frame message.

> Give the ownership of the buffer back to the image frame message.

#### Pcm frame Message

| Name                                      | Link                                          |
| ----------------------------------------- | --------------------------------------------- |
| pcm\_frame\_t::create()                   | `Link <pcm_frame_t::create>`                  |
| pcm\_frame\_t::get\_timestamp             | `Link <pcm_frame_t::get_timestamp>`           |
| pcm\_frame\_t::set\_timestamp             | `Link <pcm_frame_t::set_timestamp>`           |
| pcm\_frame\_t::get\_sample\_rate          | `Link <pcm_frame_t::get_sample_rate>`         |
| pcm\_frame\_t::set\_sample\_rate          | `Link <pcm_frame_t::set_sample_rate>`         |
| pcm\_frame\_t::get\_channel\_layout       | `Link <pcm_frame_t::get_channel_layout>`      |
| pcm\_frame\_t::set\_channel\_layout       | `Link <pcm_frame_t::set_channel_layout>`      |
| pcm\_frame\_t::get\_samples\_per\_channel | `Link <pcm_frame_t::get_samples_per_channel>` |
| pcm\_frame\_t::set\_samples\_per\_channel | `Link <pcm_frame_t::set_samples_per_channel>` |
| pcm\_frame\_t::get\_bytes\_per\_sample    | `Link <pcm_frame_t::get_bytes_per_sample>`    |
| pcm\_frame\_t::set\_bytes\_per\_sample    | `Link <pcm_frame_t::set_bytes_per_sample>`    |
| pcm\_frame\_t::get\_number\_of\_channels  | `Link <pcm_frame_t::get_number_of_channels>`  |
| pcm\_frame\_t::set\_number\_of\_channels  | `Link <pcm_frame_t::set_number_of_channels>`  |
| pcm\_frame\_t::get\_data\_fmt             | `Link <pcm_frame_t::get_data_fmt>`            |
| pcm\_frame\_t::set\_data\_fmt             | `Link <pcm_frame_t::set_data_fmt>`            |
| pcm\_frame\_t::get\_line\_size            | `Link <pcm_frame_t::get_line_size>`           |
| pcm\_frame\_t::set\_line\_size            | `Link <pcm_frame_t::set_line_size>`           |
| pcm\_frame\_t::is\_eof                    | `Link <pcm_frame_t::is_eof>`                  |
| pcm\_frame\_t::set\_is\_eof               | `Link <pcm_frame_t::set_is_eof>`              |
| pcm\_frame\_t::alloc\_buf                 | `Link <pcm_frame_t::alloc_buf>`               |
| pcm\_frame\_t::lock\_buf                  | `Link <pcm_frame_t::lock_buf>`                |
| pcm\_frame\_t::unlock\_buf                | `Link <pcm_frame_t::unlock_buf>`              |

C++ pcm frame message API

> Create a pcm frame message.

> Get/set the timestamp of the pcm frame.

> Get/set the sample rate of the pcm frame.

> Get/set the channel layout of the pcm frame.

> Get/set the samples per channel of the pcm frame.

> Get/set the bytes per sample of the pcm frame.

> Get/set the number of channels of the pcm frame.

> Get/set the data format of the pcm frame.

> Get/set line size of the pcm frame.

> Get/set the end of file flag of the pcm frame.

> Allocate a buffer for the pcm frame.

> Borrow the ownership of the buffer from the pcm frame message.

> Give the ownership of the buffer back to the pcm frame message.

#### Addon

| Name                                            | Link                                               |
| ----------------------------------------------- | -------------------------------------------------- |
| RTE\_CPP\_REGISTER\_ADDON\_AS\_EXTENSION        | `Link <RTE_CPP_REGISTER_ADDON_AS_EXTENSION>`       |
| RTE\_CPP\_REGISTER\_ADDON\_AS\_EXTENSION\_GROUP | `Link <RTE_CPP_REGISTER_ADDON_AS_EXTENSION_GROUP>` |

C++ addon API

> Register a C++ class as an RTE extension addon.

> Register a C++ class as an RTE extension group addon.

#### App

| Name                         | Link                      |
| ---------------------------- | ------------------------- |
| app\_t::run                  | `Link <app_t::run>`       |
| app\_t::close                | `Link <app_t::close>`     |
| app\_t::wait                 | `Link <app_t::wait>`      |
| Callback: app\_t::on\_init   | `Link <app_t::on_init>`   |
| Callback: app\_t::on\_deinit | `Link <app_t::on_deinit>` |

C++ app API

> Run the app.

> Close the app.

> Wait for the app to close.

#### Extension

| Name                           | Link                                 |
| ------------------------------ | ------------------------------------ |
| extension\_t::on\_init         | `Link <extension_t::on_init>`        |
| extension\_t::on\_deinit       | `Link <extension_t::on_deinit>`      |
| extension\_t::on\_start        | `Link <extension_t::on_start>`       |
| extension\_t::on\_stop         | `Link <extension_t::on_stop>`        |
| extension\_t::on\_cmd          | `Link <extension_t::on_cmd>`         |
| extension\_t::on\_data         | `Link <extension_t::on_data>`        |
| extension\_t::on\_pcm\_frame   | `Link <extension_t::on_pcm_frame>`   |
| extension\_t::on\_image\_frame | `Link <extension_t::on_image_frame>` |

C++ extension API

#### Extension Group

| Name                                         | Link                                              |
| -------------------------------------------- | ------------------------------------------------- |
| extension\_group\_t::on\_init                | `Link <extension_group_t::on_init>`               |
| extension\_group\_t::on\_deinit              | `Link <extension_group_t::on_deinit>`             |
| extension\_group\_t::on\_create\_extensions  | `Link <extension_group_t::on_create_extensions>`  |
| extension\_group\_t::on\_destroy\_extensions | `Link <extension_group_t::on_destroy_extensions>` |

C++ extension group API

#### Metadata Info

| Name                   | Link                          |
| ---------------------- | ----------------------------- |
| metadata\_info\_t::set | `Link <metadata_info_t::set>` |

C+ + metadata info API

> Set the metadata info.

#### TEN Proxy

| Name                               | Link                                    |
| ---------------------------------- | --------------------------------------- |
| rte\_proxy\_t::rte\_proxy\_t       | `Link <rte_proxy_t::rte_proxy_t>`       |
| rte\_proxy\_t::acquire\_lock\_mode | `Link <rte_proxy_t::acquire_lock_mode>` |
| rte\_proxy\_t::release\_lock\_mode | `Link <rte_proxy_t::release_lock_mode>` |
| rte\_proxy\_t::notify              | `Link <rte_proxy_t::notify>`            |

C++ rte proxy API

> Create an RTE proxy instance from a RTE instance.

> Acquire the lock mode.

> Release the lock mode.

> Enable the `notify_func` to be called in the RTE extension thread.

TEN

| Name                                     | Link                                          |
| ---------------------------------------- | --------------------------------------------- |
| rte\_t::send\_cmd                        | `Link <rte_t::send_cmd>`                      |
| rte\_t::send\_json                       | `Link <rte_t::send_json>`                     |
| rte\_t::send\_data                       | `Link <rte_t::send_data>`                     |
| rte\_t::send\_image\_frame               | `Link <rte_t::send_image_frame>`              |
| rte\_t::send\_pcm\_frame                 | `Link <rte_t::send_pcm_frame>`                |
| rte\_t::return\_result\_directly         | `Link <rte_t::return_result_directly>`        |
| rte\_t::return\_result                   | `Link <rte_t::return_result>`                 |
| rte\_t::is\_property\_exist              | `Link <rte_t::is_property_exist>`             |
| rte\_t::get\_property\_uint8             | `Link <rte_t::get_property_uint8>`            |
| rte\_t::get\_property\_uint16            | `Link <rte_t::get_property_uint16>`           |
| rte\_t::get\_property\_uint32            | `Link <rte_t::get_property_uint32>`           |
| rte\_t::get\_property\_uint64            | `Link <rte_t::get_property_uint64>`           |
| rte\_t::get\_property\_int8              | `Link <rte_t::get_property_int8>`             |
| rte\_t::get\_property\_int16             | `Link <rte_t::get_property_int16>`            |
| rte\_t::get\_property\_int32             | `Link <rte_t::get_property_int32>`            |
| rte\_t::get\_property\_int64             | `Link <rte_t::get_property_int64>`            |
| rte\_t::get\_property\_float32           | `Link <rte_t::get_property_float32>`          |
| rte\_t::get\_property\_float64           | `Link <rte_t::get_property_float64>`          |
| rte\_t::get\_property\_string            | `Link <rte_t::get_property_string>`           |
| rte\_t::get\_property\_bool              | `Link <rte_t::get_property_bool>`             |
| rte\_t::get\_property\_ptr               | `Link <rte_t::get_property_ptr>`              |
| rte\_t::get\_property\_buf               | `Link <rte_t::get_property_buf>`              |
| rte\_t::get\_property\_to\_json          | `Link <rte_t::get_property_to_json>`          |
| rte\_t::set\_property\_from\_json        | `Link <rte_t::set_property_from_json>`        |
| rte\_t::is\_cmd\_connected               | `Link <rte_t::is_cmd_connected>`              |
| rte\_t::addon\_create\_extension\_async  | `Link <rte_t::addon_create_extension_async>`  |
| rte\_t::addon\_destroy\_extension\_async | `Link <rte_t::addon_destroy_extension_async>` |
| rte\_t::on\_init\_done                   | `Link <rte_t::on_init_done>`                  |
| rte\_t::on\_deinit\_done                 | `Link <rte_t::on_deinit_done>`                |
| rte\_t::on\_start\_done                  | `Link <rte_t::on_start_done>`                 |
| rte\_t::on\_stop\_done                   | `Link <rte_t::on_stop_done>`                  |
| rte\_t::on\_create\_extensions\_done     | `Link <rte_t::on_create_extensions_done>`     |
| rte\_t::on\_destroy\_extensions\_done    | `Link <rte_t::on_destroy_extensions_done>`    |
| rte\_t::get\_attached\_target            | `Link <rte_t::get_attached_target>`           |

C++ TEN API

> Send the cmd with a response handler.
>
> When the sending action is successful, the unique\_ptr will be released to represent that the ownership of the cmd has been transferred to the TEN runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the cmd remains with the user.
>
> The type of response\_handler\_func\_t is void(rte\_t &, std::unique\_ptr\<cmd\_result\_t>)

> Send the command created from the json string without a response handler.

> Send the command created from the json string with a response handler.
>
> The type of response\_handler\_func\_t is void(rte\_t &, std::unique\_ptr\<cmd\_result\_t>)

> Send the data message to the TEN.
>
> When the sending action is successful, the unique\_ptr will be released to represent that the ownership of the data has been transferred to the TEN runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the data remains with the user.

> Send the image frame to the TEN.
>
> When the sending action is successful, the unique\_ptr will be released to represent that the ownership of the frame has been transferred to the TEN runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the frame remains with the user.

> Send the PCM frame to the TEN.
>
> When the sending action is successful, the unique\_ptr will be released to represent that the ownership of the frame has been transferred to the TEN runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the frame remains with the user.

> Return the status command directly.
>
> When the returning action is successful, the unique\_ptr will be released to represent that the ownership of the cmd has been transferred to the RTE runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the cmd remains with the user.

> Return the status command corresponding to the target command.
>
> When the returning action is successful, the unique\_ptr will be released to represent that the ownership of the cmd has been transferred to the TEN runtime. Conversely, if the sending action fails, the unique\_ptr will not perform any action, indicating that the ownership of the cmd remains with the user.

> Check if the property exists. path can not be empty.

> Get the property from the TEN in JSON format. path can not be empty.

> Set the property in the TEN from JSON string. path can not be empty.

> Check if the command is connected in the graph.

> Create an TEN extension instance with the specified `instance_name` from the specified addon specified with the `addon_name` asynchronously.

> Destroy an TEN extension instance.

> Notify the TEN that the `on_init` callback is done.

> Notify the TEN that the `on_deinit` callback is done.

> Notify the TEN that the `on_start` callback is done.

> Notify the TEN that the `on_stop` callback is done.

> Notify the TEN that the `on_create_extensions` callback is done.

> Notify the TEN that the `on_destroy_extensions` callback is done.

> Get the attached target.

### Golang

#### Message

| Name                          | Link                                     |
| ----------------------------- | ---------------------------------------- |
| msg::GetType                  | `Link <go_msg_GetType>`                  |
| msg::GetName                  | `Link <go_msg_GetName>`                  |
| msg::ToJSON                   | `Link <go_msg_ToJSON>`                   |
| msg::GetPropertyInt8          | `Link <go_msg_GetPropertyInt8>`          |
| msg::GetPropertyInt16         | `Link <go_msg_GetPropertyInt16>`         |
| msg::GetPropertyInt32         | `Link <go_msg_GetPropertyInt32>`         |
| msg::GetPropertyInt64         | `Link <go_msg_GetPropertyInt64>`         |
| msg::GetPropertyUint8         | `Link <go_msg_GetPropertyUint8>`         |
| msg::GetPropertyUint16        | `Link <go_msg_GetPropertyUint16>`        |
| msg::GetPropertyUint32        | `Link <go_msg_GetPropertyUint32>`        |
| msg::GetPropertyUint64        | `Link <go_msg_GetPropertyUint64>`        |
| msg::GetPropertyBool          | `Link <go_msg_GetPropertyBool>`          |
| msg::GetPropertyPtr           | `Link <go_msg_GetPropertyPtr>`           |
| msg::GetPropertyString        | `Link <go_msg_GetPropertyString>`        |
| msg::GetPropertyBytes         | `Link <go_msg_GetPropertyBytes>`         |
| msg::GetPropertyToJSONBytes   | `Link <go_msg_GetPropertyToJSONBytes>`   |
| msg::SetPropertyString        | `Link <go_msg_SetPropertyString>`        |
| msg::SetPropertyBytes         | `Link <go_msg_SetPropertyBytes>`         |
| msg::SetProperty              | `Link <go_msg_SetProperty>`              |
| msg::SetPropertyFromJSONBytes | `Link <go_msg_SetPropertyFromJSONBytes>` |

Golang message API

**GetType() MsgType**

Get the message type.

**GetName() (string, error)**

Get the name of the message.

**ToJSON() string**

Get the JSON string of the message.

**GetPropertyInt8(path string) (int8, error)**

Get the property from the message in int8 type.

**GetPropertyInt16(path string) (int16, error)**

Get the property from the message in int16 type.

**GetPropertyInt32(path string) (int32, error)**

Get the property from the message in int32 type.

**GetPropertyInt64(path string) (int64, error)**

Get the property from the message in int64 type.

**GetPropertyUint8(path string) (uint8, error)**

Get the property from the message in uint8 type.

**GetPropertyUint16(path string) (uint16, error)**

Get the property from the message in uint16 type.

**GetPropertyUint32(path string) (uint32, error)**

Get the property from the message in uint32 type.

**GetPropertyUint64(path string) (uint64, error)**

Get the property from the message in uint64 type.

**GetPropertyBool(path string) (bool, error)**

Get the property from the message in bool type.

**GetPropertyPtr(path string) (any, error)**

Get the property from the message in ptr type.

**GetPropertyString(path string) (string, error)**

Get the property from the message in string type.

**GetPropertyBytes(path string) (\[]byte, error)**

Get the property from the message in bytes type.

**GetPropertyToJSONBytes(path string) (\[]byte, error)**

Get the property from the message in JSON bytes type.

**SetPropertyString(path string, value string) error**

Set the property in string type.

**SetPropertyBytes(path string, value \[]byte) error**

Set the property in bytes type.

**SetProperty(path string, value any) error**

Set the property.

**SetPropertyFromJSONBytes(path string, value \[]byte) error**

SEt the property from the JSON bytes.

#### Command

| Name                | Link                                             |
| ------------------- | ------------------------------------------------ |
| NewCmd              | `Link <go_custom_cmd_NewCustomCmd>`              |
| NewCmdFromJSONBytes | `Link <go_custom_cmd_NewCustomCmdFromJSONBytes>` |

Golang custom command API

**NewCmd(cmdName string) (Cmd, error)**

Create a new custom command width the specified command name.

**NewCmdFromJSONBytes(data \[]byte) (Cmd, error)**

Create a new custom command from the specified JSON bytes.

#### Status Command

| Name                     | Link                                |
| ------------------------ | ----------------------------------- |
| NewCmdResult             | `Link <go_cmd_result_NewCmdResult>` |
| CmdResult::GetStatusCode | `Link <go_cmd_result_StatusCode>`   |

Golang status command API

**NewCmdResult(statusCode StatusCode, detail any) (CmdResult, error)**

Create a new status command.

**GetStatusCode() (StatusCode, error)**

Get the status code from the status command.

#### Data Message

| Name                           | Link                         |
| ------------------------------ | ---------------------------- |
| [Data::NewData](Data::NewData) | `Link <go_data_msg_NewData>` |
| [Data::GetBuf](Data::GetBuf)   | `Link <go_data_msg_GetBuf>`  |

Golang data message API

**NewData(bytes \[]byte) (Data, error)**

Create a new data message.

**GetBuf() (\[]byte, error)**

Get the data buffer from the data message. Note that this function performs a deep copy of the data buffer.

#### Image Frame Message

#### Pcm Frame Message

#### Error

> Default errno, for those users only care error msgs.

> Invalid json.

> Invalid argument.

> Invalid type.

| Name             | Link                     |
| ---------------- | ------------------------ |
| TENError::Error  | `Link <go_error_Error>`  |
| TENError::ErrNo  | `Link <go_error_ErrNo>`  |
| TENError::ErrMsg | `Link <go_error_ErrMsg>` |

Golang error API

**Error() string**

**ErrNo() uint32**

Get the error number.

**ErrMsg() string**

Get the error message.

#### Addon

| Name                          | Link                                            |
| ----------------------------- | ----------------------------------------------- |
| Addon::OnInit                 | `Link <go_addon_OnInit>`                        |
| Addon::OnDeinit               | `Link <go_addon_OnDeinit>`                      |
| Addon::OnCreateInstance       | `Link <go_addon_OnCreateInstance>`              |
| RegisterAddonAsExtension      | `Link <go_addon_RegisterAddonAsExtension>`      |
| RegisterAddonAsExtensionGroup | `Link <go_addon_RegisterAddonAsExtensionGroup>` |
| UnloadAllAddons               | `Link <go_addon_UnloadAllAddons>`               |
| NewDefaultExtensionAddon      | `Link <go_addon_NewDefaultExtensionAddon>`      |
| NewDefaultExtensionGroupAddon | `Link <go_addon_NewDefaultExtensionGroupAddon>` |

Golang addon API

**OnInit(rte Rte, manifest MetadataInfo, property MetadataInfo)**

Initialize the addon.

**OnDeinit(rte Rte)**

De-initialize the addon.

**OnCreateInstance(rte Rte, name string) any**

Create an instance of the addon.

**RegisterAddonAsExtension(name string, addon \*Addon) error**

Register the addon as an extension addon to the TEN runtime environment.

**RegisterAddonAsExtensionGroup(name string, addon \*Addon) error**

Register the addon as an extension group addon to the TEN runtime environment.

**UnloadAllAddons() error**

Un-register all the addons from the TEN runtime environment.

**NewDefaultExtensionAddon(constructor func(name string) Extension) \*Addon**

Create a new default extension addon.

**NewDefaultExtensionGroupAddon(constructor func(name string) ExtensionGroup) \*Addon**

Create a new default extension group addon.

#### App

| Name          | Link                     |
| ------------- | ------------------------ |
| NewApp        | `Link <go_app_NewApp>`   |
| App::OnInit   | `Link <go_app_OnInit>`   |
| App::OnDeinit | `Link <go_app_OnDeinit>` |
| App::Run      | `Link <go_app_Run>`      |
| App::Close    | `Link <go_app_Close>`    |
| App::Wait     | `Link <go_app_Wait>`     |

Golang app API

**NewApp(iApp IApp) (App, error)**

Create a new app.

**OnInit(rte Rte, manifest MetadataInfo, property MetadataInfo)**

Initialize the app.

**OnDeinit(rte Rte)**

De-initialize the app.

**Run(runInBackground bool)**

Run the app.

**Close()**

Close the app.

**Wait()**

Wait the app to be closed.

#### Extension

| Name                     | Link                               |
| ------------------------ | ---------------------------------- |
| Extension::OnInit        | `Link <go_extension_OnInit>`       |
| Extension::OnStart       | `Link <go_extension_OnStart>`      |
| Extension::OnStop        | `Link <go_extension_OnStop>`       |
| Extension::OnDeinit      | `Link <go_extension_OnDeinit>`     |
| Extension::OnCmd         | `Link <go_extension_OnCmd>`        |
| Extension::OnData        | `Link <go_extension_OnData>`       |
| Extension::OnImageFrame  | `Link <go_extension_OnImageFrame>` |
| Extension::OnPcmFrame    | `Link <go_extension_OnPcmFrame>`   |
| Extension::WrapExtension | `Link <go_extension_NewExtension>` |

Golang extension API

**OnInit(rte Rte, manifest MetadataInfo, property MetadataInfo)**

**OnStart(rte Rte)**

**OnStop(rte Rte)**

**OnDeinit(rte Rte)**

**OnCmd(rte Rte, cmd Cmd)**

**OnData(rte Rte, data Data)**

**OnImageFrame(rte Rte, imageFrame ImageFrame)**

**OnPcmFrame(rte Rte, pcmFrame PcmFrame)**

**WrapExtension(ext Extension, name string) \*extension**

Create a new extension instance with the specified name.

#### Extension Group

| Name                                | Link                                            |
| ----------------------------------- | ----------------------------------------------- |
| ExtensionGroup::OnInit              | `Link <go_extension_group_OnInit>`              |
| ExtensionGroup::OnDeinit            | `Link <go_extension_group_OnDeinit>`            |
| ExtensionGroup::OnCreateExtensions  | `Link <go_extension_group_OnCreateExtensions>`  |
| ExtensionGroup::OnDestroyExtensions | `Link <go_extension_group_OnDestroyExtensions>` |
| WrapExtensionGroup                  | `Link <go_extension_group_NewExtensionGroup>`   |

Golang extension group API

**OnInit(rte Rte, manifest MetadataInfo, property MetadataInfo)**

**OnDeinit(rte Rte)**

**OnCreateExtensions(rte Rte)**

**OnDestroyExtensions(rte Rte, extensions \[]Extension)**

**WrapExtensionGroup(extGroup ExtensionGroup, name string) \*extensionGroup**

Create a new extension group instance with the specified name.

#### Metadata Info

| Name              | Link                          |
| ----------------- | ----------------------------- |
| MetadataInfo::Set | `Link <go_metadata_info_Set>` |

Golang metadata info API

**Set(metadataType MetadataType, value string)**

Set the metadata info.

TEN

| Name                            | Link                                       |
| ------------------------------- | ------------------------------------------ |
| rte::GetPropertyInt8            | `Link <go_rte_GetPropertyInt8>`            |
| rte::GetPropertyInt16           | `Link <go_rte_GetPropertyInt16>`           |
| rte::GetPropertyInt32           | `Link <go_rte_GetPropertyInt32>`           |
| rte::GetPropertyInt64           | `Link <go_rte_GetPropertyInt64>`           |
| rte::GetPropertyUint8           | `Link <go_rte_GetPropertyUint8>`           |
| rte::GetPropertyUint16          | `Link <go_rte_GetPropertyUint16>`          |
| rte::GetPropertyUint32          | `Link <go_rte_GetPropertyUint32>`          |
| rte::GetPropertyUint64          | `Link <go_rte_GetPropertyUint64>`          |
| rte::GetPropertyBool            | `Link <go_rte_GetPropertyBool>`            |
| rte::GetPropertyPtr             | `Link <go_rte_GetPropertyPtr>`             |
| rte::GetPropertyString          | `Link <go_rte_GetPropertyString>`          |
| rte::GetPropertyBytes           | `Link <go_rte_GetPropertyBytes>`           |
| rte::GetPropertyToJSONBytes     | `Link <go_rte_GetPropertyToJSONBytes>`     |
| rte::SetPropertyString          | `Link <go_rte_SetPropertyString>`          |
| rte::SetPropertyBytes           | `Link <go_rte_SetPropertyBytes>`           |
| rte::SetProperty                | `Link <go_rte_SetProperty>`                |
| rte::SetPropertyAsync           | `Link <go_rte_SetPropertyAsync>`           |
| rte::SetPropertyFromJSONBytes   | `Link <go_rte_SetPropertyFromJSONBytes>`   |
| rte::ReturnResult               | `Link <go_rte_ReturnResult>`               |
| rte::ReturnResultDirectly       | `Link <go_rte_ReturnResultDirectly>`       |
| rte::SendJSON                   | `Link <go_rte_SendJSON>`                   |
| rte::SendJSONBytes              | `Link <go_rte_SendJSONBytes>`              |
| rte::SendCmd                    | `Link <go_rte_SendCmd>`                    |
| rte::SendData                   | `Link <go_rte_SendData>`                   |
| rte::SendImageFrame             | `Link <go_rte_SendImageFrame>`             |
| rte::SendPcmFrame               | `Link <go_rte_SendPcmFrame>`               |
| rte::OnStartDone                | `Link <go_rte_OnStartDone>`                |
| rte::OnStopDone                 | `Link <go_rte_OnStopDone>`                 |
| rte::OnInitDone                 | `Link <go_rte_OnInitDone>`                 |
| rte::OnDeinitDone               | `Link <go_rte_OnDeinitDone>`               |
| rte::OnCreateExtensionsDone     | `Link <go_rte_OnCreateExtensionsDone>`     |
| rte::OnDestroyExtensionsDone    | `Link <go_rte_OnDestroyExtensionsDone>`    |
| rte::OnCreateInstanceDone       | `Link <go_rte_OnCreateInstanceDone>`       |
| rte::IsCmdConnected             | `Link <go_rte_IsCmdConnected>`             |
| rte::AddonCreateExtensionAsync  | `Link <go_rte_AddonCreateExtensionAsync>`  |
| rte::AddonDestroyExtensionAsync | `Link <go_rte_AddonDestroyExtensionAsync>` |

Golang rte API

**GetPropertyInt8(path string) (int8, error)**

Get the property from the RTE in int8 type.

**GetPropertyInt16(path string) (int16, error)**

Get the property from the RTE in int16 type.

**GetPropertyInt32(path string) (int32, error)**

Get the property from the RTE in int32 type.

**GetPropertyInt64(path string) (int64, error)**

Get the property from the RTE in int64 type.

**GetPropertyUint8(path string) (uint8, error)**

Get the property from the RTE in uint8 type.

**GetPropertyUint16(path string) (uint16, error)**

Get the property from the RTE in uint16 type.

**GetPropertyUint32(path string) (uint32, error)**

Get the property from the RTE in uint32 type.

**GetPropertyUint64(path string) (uint64, error)**

Get the property from the RTE in uint64 type.

**GetPropertyBool(path string) (bool, error)**

Get the property from the RTE in bool type.

**GetPropertyPtr(path string) (any, error)**

Get the property from the RTE in ptr type.

**GetPropertyString(path string) (string, error)**

Get the property from the RTE in string type.

**GetPropertyBytes(path string) (\[]byte, error)**

Get the property from the RTE in bytes type.

**GetPropertyToJSONBytes(path string) (\[]byte, error)**

Get the property from the RTE in JSON bytes type.

**SetProperty(path string, value any) error**

Set the property to the RTE.

**SetPropertyString(path string, value string) error**

Set the property to the RTE in string type.

**SetPropertyBytes(path string, value \[]byte) error**

Set the property to the RTE in bytes type.

**SetPropertyFromJSONBytes(path string, value \[]byte) error**

Set the property to the RTE from the JSON bytes.

**SetPropertyAsync(path string, v any, callback func(Rte, error)) error**

Set the property to the RTE asynchronously.

**ReturnResult(statusCmd CmdResult, cmd Cmd) error**

Return the result.

**ReturnResultDirectly(statusCmd CmdResult) error**

Return the result directly.

**SendJSON(json string, handler ResponseHandler) error**

Send the JSON.

**SendJSONBytes(json \[]byte, handler ResponseHandler) error**

Send the JSON bytes.

**SendCmd(cmd Cmd, handler ResponseHandler) error**

Send the command.

**SendData(data Data) error**

Send the data.

**SendImageFrame(imageFrame ImageFrame) error**

Send the image frame.

**SendPcmFrame(pcmFrame PcmFrame) error**

Send the PCM frame.

**OnInitDone(manifest MetadataInfo, property MetadataInfo) error**

OnInit done.

**OnStartDone() error**

OnStart done.

**OnStopDone() error**

OnStop done.

**OnDeinitDone() error**

OnDeinit done.

**OnCreateExtensionsDone(extensions ...Extension) error**

OnCreateExtensions done.

**OnDestroyExtensionsDone() error**

OnDestroyExtensions done.

**OnCreateInstanceDone(instance any) error**

OnCreateInstance done.

**IsCmdConnected(cmdName string) (bool, error)**

Is the command connected.

**AddonCreateExtensionAsync(addonName string, instanceName string, callback func(rte Rte, p Extension)) error**

Create an extension from the addon specified by the addon name and instance name.

**AddonDestroyExtensionAsync(ext Extension, callback func(rte Rte)) error**

Destroy a specified extension.
