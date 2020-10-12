---
title: Azure Service Fabric CLI - sfctl store
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包含用于对群集映像存储执行文件级别操作的命令列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257064"
---
# <a name="sfctl-store"></a>sfctl store
针对群集映像存储执行基本文件级别操作。

## <a name="commands"></a>命令

|Command|说明|
| --- | --- |
| delete | 删除现有的映像存储内容。 |
| root-info | 获取映像存储根目录中的内容信息。 |
| stat | 获取映像存储内容信息。 |

## <a name="sfctl-store-delete"></a>sfctl store delete
删除现有的映像存储内容。

删除在给定图像存储相对路径中找到的现有映像存储内容。 预配已上传的应用程序包后，可以使用此命令删除这些包。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --content-path [必需] | 映像存储中文件或文件夹在其根目录中的相对路径。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
获取映像存储根目录中的内容信息。

返回有关映像存储根目录中的映像存储内容的信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-store-stat"></a>sfctl store stat
获取映像存储内容信息。

返回有关指定 contentPath 中的映像存储内容的信息。 contentPath 相对于映像存储的根目录。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --content-path [必需] | 映像存储中文件或文件夹在其根目录中的相对路径。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
