---
title: Azure 服务结构 CLI-sfctl 网格部署
description: 了解 sfctl，Azure 服务结构命令行接口。 包括用于创建服务结构网格资源的命令列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906023"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
创建 Service Fabric 网格资源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| create | 创建 Service Fabric 网格资源的部署。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
创建 Service Fabric 网格资源的部署。

### <a name="arguments"></a>自变量

|参数|描述|
| --- | --- |
| --input-yaml-files [必需] | 包含 yaml 文件的所有 yaml 文件或目录的相对或绝对路径（递归）的逗号分隔相对或绝对文件路径。 |
| --parameters | 任人文件或 json 对象的相对或绝对路径，其中包含需要重写的参数。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

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

通过重写直接作为 json 对象传递的参数，整合和部署目录中的所有资源以进行群集
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>后续步骤
- [设置](service-fabric-cli.md)服务结构 CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
