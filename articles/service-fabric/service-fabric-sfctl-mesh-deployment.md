---
title: Azure Service Fabric CLI- sfctl mesh deployment | Microsoft Docs
description: 介绍了 Service Fabric CLI sfctl mesh deployment 命令。
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b25384d8f3c6e41b6c5cca723d41b79f00b17494
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283833"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
创建 Service Fabric 网格资源。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| create | 创建 Service Fabric 网格资源的部署。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
创建 Service Fabric 网格资源的部署。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --input-yaml-files [必需] | 所有 yaml 文件的逗号分隔相对/绝对文件路径，或者包含 yaml 文件的目录（递归）的相对/绝对路径。 |
| --parameters | 包含需要覆盖的参数的 yaml 文件或 json 对象的相对/绝对路径。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

### <a name="examples"></a>示例

通过覆盖 yaml 文件中提到的参数将所有资源整合并部署到群集中

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

通过覆盖 yaml 文件中提到的参数将某个目录中的所有资源整合并部署到群集中

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

通过覆盖直接作为 json 对象传递的参数将某个目录中的所有资源整合并部署到群集中

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。