---
title: Azure Service Fabric CLI- sfctl property | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl property 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763922"
---
# <a name="sfctl-property"></a>sfctl property
在 Service Fabric 名称下存储和查询属性。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| delete | 删除指定的 Service Fabric 属性。 |
| get | 获取指定的 Service Fabric 属性。 |
| list | 获取给定名称下的所有 Service Fabric 属性的信息。 |
| put | 创建或更新 Service Fabric 属性。 |

## <a name="sfctl-property-delete"></a>sfctl property delete
删除指定的 Service Fabric 属性。

删除给定名称下的指定 Service Fabric 属性。 只能删除已创建的属性。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --name-id       [必需] | Service Fabric 名称，不带“fabric\:”URI 方案。 |
| --property-name [必需] | 指定要获取的属性的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-property-get"></a>sfctl property get
获取指定的 Service Fabric 属性。

获取给定名称下的指定 Service Fabric 属性。 这将始终返回值和元数据。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --name-id       [必需] | Service Fabric 名称，不带“fabric\:”URI 方案。 |
| --property-name [必需] | 指定要获取的属性的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-property-list"></a>sfctl property list
获取给定名称下的所有 Service Fabric 属性的信息。

一个 Service Fabric 名称可具有一个或多个存储自定义信息的命名属性。 此操作将有关这些属性的信息获取到分页列表中。 信息包括有关其中每个属性的名称、值和元数据。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --name-id [必需] | Service Fabric 名称，不带“fabric\:”URI 方案。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --include-values | 允许指定是否包括返回的属性的值。 如果值应随元数据一起返回，则为 true；如果为 false，则仅返回属性元数据。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-property-put"></a>sfctl property put
创建或更新 Service Fabric 属性。

创建或更新给定名称下的指定 Service Fabric 属性。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --name-id       [必需] | Service Fabric 名称，不带“fabric\:”URI 方案。 |
| --property-name [必需] | Service Fabric 属性的名称。 |
| --value         [必需] | 介绍 Service Fabric 属性值。 这是一个 JSON 字符串。 <br><br> Json 字符串有两个字段：数据的“Kind”和数据的“Value”。 “Kind”的值必须是要出现在 JSON 字符串中的第一个项，并且可以是以下值：“Binary”、“Int64”、“Double”、“String”或“Guid”。 该值应当对给定的类型可序列化。 “Kind”和“Data”的值应以字符串形式提供。 |
| --custom-id-type | 属性的自定义类型 ID。 使用此属性，用户就能够标记属性值的类型。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。