# Type System

## Types

TEN framework Type System 是 TEN framework 内用来定义 value 的数据类型系统. 同时, 开发者可以通过 TEN schema 来声明 message 或者 extension 的 property 的类型.

TEN framework Type System 包含 基本类型 和 复合类型. 其中, 基本类型 包括如下几种:

| Type | Description | C++ Type | Go Type | Python Type |
|------|-------------|----------|---------|-------------|
| int8 | A 8-bit signed integer. | int8_t | int8 | int  |
| int16 | A 16-bit signed integer. | int16_t | int16 |   |
| int32 | A 32-bit signed integer. | int32_t | int32 |   |
| int64 | A 64-bit signed integer. | int64_t | int64 |   |
| uint8 | A 8-bit unsigned integer. | uint8_t | uint8 |   |
| uint16 | A 16-bit unsigned integer. | uint16_t | uint16 |   |
| uint32 | A 32-bit unsigned integer. | uint32_t | uint32 |   |
| uint64 | A 64-bit unsigned integer. | uint64_t | uint64 |   |
| float32 | A single precision (32-bit) IEEE 754 floating-point number. | float | float32 |
| float64 | A double-precision (64-bit) IEEE 754 floating-point number. | double | float64 |
| string | An Unicode character sequence. | std::string / char\* | string |
| buf | A sequence of 8-bit unsigned bytes. | uint8_t\* | \[\]byte |
| bool | A binary value, either true or false. | bool | bool |
| ptr | A pointer to a memory address. | void\* | unsafe.Pointer |

复合类型 包括如下几种:

| Type | Description |
|----|----|
| array | A collection of elements of the same type. |
| object | Represents a complex key/value pair. The type of key is always string. |

对于 基本类型, 可以通过如 get_property() / set_property() 等方法来获取或者设置. 例如:

``` cpp
// Get property value
int32_t value = cmd.get_property_int32("property_name");

// Set property value
cmd.set_property("property_name", 100);
```

而对于 复合类型, 一般的情况是需要通过配合相关的序列化方法. 例如:

``` Golang
type MyProp struct {
    Name string `json:"name"`
}

var prop MyProp
bytes, _ := json.Marshal(&prop)
cmd.SetPropertyFromJSONBytes("property_name", bytes)
```

## Type and Schema

如果指定了 TEN Schema, 则 property 的 type 将根据相应的 TEN Schema 来设定. 如果未指定 TEN Schema, property 的 type 将在首次赋值时根据当时的赋值类型来确定. 例如如果首次赋值时指定的是 int32_t, 则该 property 的 type 就是 int32_t; 如果首次赋值是使用 JSON 赋值的方式, 则其 type 就按照 JSON 的处理模式来决定.

## Conversion Rules

### Safe and Must-Succeed Conversion

将低精度类型转换成高精度类型是安全的, 因为高精度类型可以完全容纳低精度类型的值, 而不会造成数据丢失. 这种自动转换被称为 Safe Conversion. 例如, 当尝试以 int32 获取一个 int8 类型的 property 时, TEN type system 会自动将 property 的类型转换成 int32.

在 TEN Type System 中, Safe Conversion 的规则如下. 原則有二:

1. int 内从低精度转高精度
2. uint 内从低精度转高精度
3. float 内从低精度转高精度

| From    | To (Allowed)             |
|---------|--------------------------|
| int8    | int16 / int32 / int64    |
| int16   | int32 / int64            |
| int32   | int64                    |
| uint8   | uint16 / uint32 / uint64 |
| uint16  | uint32 / uint64          |
| uint32  | uint64                   |
| float32 | float64                  |

TEN Type Safe Conversion Rules

例如:

``` cpp
// Set property value. The type of `property_name` in TEN Runtime is `int32`.
cmd.set_property("property_name", 100);

// Get property value. Correct.
int32_t value = cmd.get_property_int32("property_name");

// Get property value. Correct. TEN Type System will automatically convert the type to `int64`.
int64_t value2 = cmd.get_property_int64("property_name");

// Get property value. Incorrect, an error will be thrown.
int16_t error_type = cmd.get_property_int16("property_name");
```

> [!NOTE]
> 目前, TEN runtime 只有在 get_property() 的时候, 才会使用 Safe Conversion.

### Unsafe and Might-Fail Conversion

将高精度类型转换成低精度类型是不安全的, 因为高精度类型的值可能会超出低精度类型的范围, 从而导致数据丢失. 这种转换被称为 Unsafe Conversion. TEN runtime 在进行 Unsafe Conversion 的时候, 会判断是否会溢出 (Overflow). 如果溢出, 那么 TEN Type System 会抛出异常.

在 TEN Type System 中, Unsafe Conversion 的规则如下. 原则为:

1. 将 int64 转成低精度的 int 时.
2. 将 int64 转成任意精度的 uint 时.
3. 将 float64 转成 float32 时.

| From    | To      | Correct Value Range of From |
|---------|---------|------------------------------------------------------------|
| int64   | int8    | \[-2^7, 2^7 - 1\]                                          |
| int64   | int16   | \[-2^15, 2^15 - 1\]                                        |
| int64   | int32   | \[-2^31, 2^31 - 1\]                                        |
| int64   | uint8   | \[0, 2^7 - 1\]                                             |
| int64   | uint16  | \[0, 2^15 - 1\]                                            |
| int64   | uint32  | \[0, 2^31 - 1\]                                            |
| int64   | uint64  | \[0, 2^63 - 1\]                                            |
| float64 | float32 | \[-3.4028234663852886e+38, 3.4028234663852886e+38\]        |

TEN Type Unsafe Conversion Rules

需要注意的是, TEN runtime 只在将 JSON document 反序列化成 TEN property 的时候, 且 TEN property 的 TEN schema 存在的情况下, 才会进行 Unsafe Conversion. 比如:

- 加载 property.json 时.

  对于其中定义的整数类型, 默认会被解析为 int64 类型; 浮点数类型, 默认会被解析为 float64 类型. TEN Type System 会按照上述规则进行 Unsafe Conversion.

- 调用如 set_property_from_json() 方法时.

  传入序列化后的 json 字符串时, TEN Type System 也会按照上述规则进行 Unsafe Conversion.

### Unsupported Conversion

TEN Type System 不对如下情况进行 \`Conversion\`:

- enum -\> int.

  尽管 c++ 规范中, enum value 就是 int 类型, 需要开发者先进行 Explicit Conversion, 如 static_cast\(enum_value).

- int -\> bool.

- set_property / get_property 中的类型不支持 char

  需要开发者先进行 Explicit Conversion, 如 static_cast\(char_value).

- \[\]byte -\> string. 尽管 Go String 的底层实现是 \[\]byte.

- command conversion 时, 如果 source 跟 destination 的 type 不相符, command conversion 会失败, source extension 会收到 command 传不出去的 error cmd result.
