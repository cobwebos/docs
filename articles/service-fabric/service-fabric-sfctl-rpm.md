---
title: Azure Service Fabric CLI-sfctl rpm
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含修复管理器服务的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904942"
---
# <a name="sfctl-rpm"></a>sfctl rpm
查询并向修复管理器服务发送命令。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| approve-force | 强制批准给定的修复任务。 |
| delete | 删除已完成的修复任务。 |
| list | 获取与给定筛选器匹配的修复任务列表。 |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
强制批准给定的修复任务。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --task-id [必需] | 修复任务的 ID。 |
| --version | 修复任务的当前版本号。 如果值为非零，则仅当此值与修复任务的实际当前版本匹配时，请求才会成功。 如果值为零，则不执行版本检查。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
删除已完成的修复任务。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --task-id [必需] | 要删除的已完成修复任务的 ID。 |
| --version | 修复任务的当前版本号。 如果值为非零，则仅当此值与修复任务的实际当前版本匹配时，请求才会成功。 如果值为零，则不执行版本检查。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
获取与给定筛选器匹配的修复任务列表。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --executor-filter | 应在列表中包含其声明任务的修复执行程序的名称。 |
| --state-filter | 以下值的按位 OR 运算，指定应在结果列表中包含哪些任务状态。 <ul><li>1 - 已创建</li><li>2-声明</li><li>4-准备</li><li>8-已批准</li><li>16-正在执行</li><li>32-正在还原</li><li>64 - 已完成</li></ul>
| --task-id-filter | 要匹配的修复任务 ID 前缀。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
