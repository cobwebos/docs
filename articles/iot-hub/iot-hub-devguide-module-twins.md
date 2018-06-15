---
title: 了解 Azure IoT 中心模块孪生 | Microsoft Docs
description: 开发人员指南 - 使用模块孪生在 IoT 中心与设备之间同步状态和配置数据
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 71d762b6f1c199db17058ac107aad7a0b3260ae7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633489"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>在 IoT 中心内了解并使用模块孪生

本文假设读者事先已阅读[了解并在 IoT 中心内使用设备孪生][lnk-devguide-device-twins]。 在 IoT 中心的每个设备标识下，最多可以创建 20 个模块标识。 每个模块标识隐式生成模块孪生。 模块孪生与设备孪生非常类似，是存储模块状态信息（例如元数据、配置和条件）的 JSON 文档。 Azure IoT 中心为连接到 IoT 中心的每个模块保留一个模块孪生。 

在设备端，可以使用 IoT 中心设备 SDK 创建模块，其中每个模块与 IoT 中心单独建立连接。 这样，便可对设备上的不同组件使用不同的命名空间。 例如，某个自动贩卖机包含三个不同的传感器。 每个传感器由公司的不同部门控制。 可为每个传感器创建一个模块。 这样，每个部门只能向他们控制的传感器发送作业或直接方法，避免发生冲突和用户失误。

 模块标识和模块孪生提供的功能与设备标识和设备孪生相同，但前者的粒度更细。 这种更高的粒度级可让有能力的设备（例如基于操作系统的设备，或管理多个组件的固件设备）隔离其中每个组件的配置和状态。 与包含模块化软件组件的 IoT 设备结合使用时，模块标识和模块孪生能够提供管理关注点分离。 推出模块孪生的正式版后，我们将会支持模块孪生级别的所有设备孪生功能。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本文介绍：

* 模块孪生的结构：标记、所需的属性和报告的属性。
* 模块和后端可在模块孪生上执行的操作。

有关使用报告的属性、设备到云的消息或文件上传的指导，请参阅[设备到云的通信指南][lnk-d2c-guidance]。
有关使用所需的属性、直接方法或云到设备的消息的指导，请参阅[云到设备的通信指南][lnk-c2d-guidance]。

## <a name="module-twins"></a>模块孪生
模块孪生存储具有以下用途的模块相关信息：

* 设备和 IoT 中心上的模块可以使用这些信息来同步模块状态和配置。
* 解决方案后端可以使用这些信息来查询和定位长时间运行的操作。

模块孪生的生命周期链接到相应的[模块标识][lnk-identity]。 在 IoT 中心创建或删除模块标识时，将隐式创建和删除模块孪生。

模块孪生是一个 JSON 文档，其中包含：

* **标记**。 解决方案后端可从中读取和写入数据的 JSON 文档的某个部分。 标记对设备上的模块不可见。 设置的标记用于查询目的。
* **所需属性**。 与报告的属性结合使用，同步模块配置或状态。 解决方案后端可设置所需的属性，并且模块应用可进行读取。 此外，当所需的属性发生更改时，模块应用可收到通知。
* **报告属性**。 与所需的属性结合使用，同步模块配置或状态。 模块应用可设置报告的属性，并且解决方案后端可进行读取和查询。
* **模块标识属性**。 模块孪生 JSON 文档的根包含[标识注册表][lnk-identity]中存储的相应模块标识的只读属性。

![][img-module-twin]

以下示例显示了一个模块孪生 JSON 文档：

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

根对象中包含模块标识属性，以及 `tags`、`reported` 和 `desired` 属性的容器对象。 `properties` 容器包含一些只读元素（`$metadata`、`$etag` 和 `$version`），[模块孪生元数据][lnk-module-twin-metadata]和[乐观并发][lnk-concurrency]部分描述了这些元素。

### <a name="reported-property-example"></a>报告属性示例
在上面的示例中，模块孪生包含模块应用报告的 `batteryLevel` 属性。 使用此属性可以根据上次报告的电池电量水平查询和操作模块。 其他示例包括让模块应用报告模块功能或连接选项。

> [!NOTE]
> 报告的属性如何简化解决方案后端获取属性最后一个已知值的方案。 如果解决方案后端需要以带时间戳事件序列（例如时间序列）的形式处理模块遥测数据，可以使用[设备到云的消息][lnk-d2c]。

### <a name="desired-property-example"></a>所需属性示例
在上面的示例中，解决方案后端和模块应用使用 `telemetryConfig` 模块孪生的所需和报告属性来同步此模块的遥测配置。 例如：

1. 解决方案后端使用所需配置值设置所需属性。 下面是包含所需属性集的文档的一部分：

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. 连接后或者首次重新连接时，模块应用会立即收到更改通知。 然后，模块应用报告更新的配置（或使用 `status` 属性报告错误状态）。 下面是报告属性的一部分：

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. 解决方案后端可以通过[查询][lnk-query]模块孪生，跟踪多个模块上的配置操作结果。

> [!NOTE]
> 为便于阅读，上述代码片段示例经过优化，演示了为模块配置及其状态进行编码的一种方式。 IoT 中心不会对模块孪生中的所需属性和报告属性施加特定的架构。
> 
> 

## <a name="back-end-operations"></a>后端操作
解决方案后端使用以下通过 HTTPS 公开的原子操作对模块孪生执行操作：

* **按 ID 检索模块孪生**。 此操作返回模块孪生文档，包括标记、所需的系统属性和报告的系统属性。
* **部分更新模块孪生**。 解决方案后端可以使用此操作部分更新模块孪生中的标记或所需属性。 部分更新以 JSON 文档的形式表示，可添加或更新任何属性。 将删除设置为 `null` 的属性。 以下示例将创建值为 `{"newProperty": "newValue"}` 的新所需属性，将现有值 `existingProperty` 覆盖为 `"otherNewValue"`，并删除 `otherOldProperty`。 不会对现有的所需属性或标记进行其他任何更改：

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **替换所需属性**。 解决方案后端可以使用此操作完全覆盖所有现有的所需属性，并使用新 JSON 文档替代 `properties/desired`。
* **替换标记**。 解决方案后端可以使用此操作完全覆盖所有现有标记，并使用新 JSON 文档替代 `tags`。
* **接收孪生通知**。 此操作允许解决方案后端在修改孪生时收到通知。 为此，IoT 解决方案需要创建一个路由，并且将“数据源”设置为等于 *twinChangeEvents*。 默认情况下，不会发送孪生通知，即，无此类路由预先存在。 如果更改速率太高，或由于其他原因（例如内部故障），IoT 中心可能会只发送一个包含所有更改的通知。 因此，如果应用程序需要可靠地审核和记录所有中间状态，则应使用设备到云消息。 孪生通知消息包括属性和正文。

    - 属性

    | 名称 | 值 |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  发送通知的时间 |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | 设备 ID |
    moduleId | 模块 的 ID |
    hubName | IoT 中心的名称 |
    operationTimestamp | [ISO8601] 操作时间戳 |
    iothub-message-schema | deviceLifecycleNotification |
    opType | “replaceTwin”或“updateTwin” |

    消息系统属性以 `'$'` 符号为前缀。

    - Body
        
    本部分包括 JSON 格式的所有孪生更改。 它使用与修补程序相同的格式，不同的是它包含所有孪生节：标记、properties.reported、properties.desired，并且它包含“$metadata”元素。 例如，

    ```json
    {
        "properties": {
            "desired": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            },
            "reported": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            }
        }
    }
    ```

上述所有操作支持[乐观并发][lnk-concurrency]，需要[安全性][lnk-security]一文中定义的 **ServiceConnect** 权限。

除了上述操作以外，解决方案后端还可以：

* 使用类似于 SQL 的 [IoT 中心查询语言][lnk-query]查询模块孪生。

## <a name="module-operations"></a>模块操作
模块应用使用以下原子操作对模块孪生执行操作：

* **检索模块孪生**。 此操作返回当前连接的模块的模块孪生文档（包括标记、所需的系统属性和报告的系统属性）。
* **部分更新报告属性**。 使用此操作可以部分更新当前连接的模块的报告属性。 此操作使用的 JSON 更新格式与解决方案后端用于部分更新所需属性的格式相同。
* **观察所需属性**。 当前连接的模块可以选择在所需属性发生更新时接收通知。 模块收到的更新格式与解决方案后端执行的更新格式相同（部分或完全替换）。

上述所有操作需要[安全性][lnk-security]一文中定义的 **ModuleConnect** 权限。

借助 [Azure IoT 设备 SDK][lnk-sdks]，可通过多种语言和平台轻松使用上述操作。

## <a name="tags-and-properties-format"></a>标记和属性格式
标记、所需的属性和报告的属性是具有以下限制的 JSON 对象：

* JSON 对象中的所有键是区分大小写的 64 字节 UTF-8 UNICODE 字符串。 允许的字符不包括 UNICODE 控制字符（段 C0 和 C1）以及 `'.'`、`' '` 和 `'$'`。
* JSON 对象中的所有值可采用以下 JSON 类型：布尔值、数字、字符串、对象。 不允许数组。 最大整数值为 4503599627370495，而最小整数值为 -4503599627370496。
* 标记、所需属性和报告属性中的所有 JSON 对象的最大嵌套深度为 5 层。 例如，以下对象是有效的：

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* 所有字符串的值的长度最多为 4 KB。

## <a name="module-twin-size"></a>模块孪生大小
IoT 中心对 `tags`、`properties/desired` 和 `properties/reported`（不包括只读元素）的各个总值强制实施 8KB 大小限制。
该大小的计算考虑到了所有字符，但不包括 UNICODE 控制字符（段 C0 和 C1），以及出现在字符串常量外部的空格。
IoT 中心拒绝将这些文档的大小增加到超出限制的所有操作，在这种情况下还会返回错误。

## <a name="module-twin-metadata"></a>模块孪生元数据
IoT 中心保留模块孪生所需属性和报告属性中每个 JSON 对象的上次更新时间戳。 时间戳采用 UTC，以 [ISO8601] 格式编码`YYYY-MM-DDTHH:MM:SS.mmmZ`。
例如：

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

将在每个级别（而不仅仅是 JSON 结构的叶级别）保留此信息，以便保留删除了对象键的更新。

## <a name="optimistic-concurrency"></a>乐观并发
标记、所需的属性和报告的属性都支持乐观并发。
标记包含一个符合 [RFC7232] 规范的 ETag，它是标记的 JSON 表示形式。 可在解决方案后端上的条件更新操作中使用 ETag 来确保一致性。

模块孪生所需的属性和报告的属性不包含 ETag，但包含一个保证可递增的 `$version` 值。 更新方可以使用类似于 ETag 的版本来强制实施更新一致性。 例如，报告的属性的模块应用，或者所需的属性的解决方案后端。

当监视代理（例如，监视所需属性的模块应用）必须协调检索操作结果与更新通知之间的资源争用时，版本也很有用。 [设备重新连接流][lnk-reconnection] 部分提供了详细信息。 

## <a name="next-steps"></a>后续步骤
要尝试本文中介绍的一些概念，请参阅以下 IoT 中心教程：

* [通过 .NET 备份和 .NET 设备开始使用 IoT 中心模块标识和模块孪生][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg
