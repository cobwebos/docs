---
title: Azure Service Fabric CLI- sfctl is | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl is 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 11c7a10c6448248ffacee2b519f48b6ca7e4e188
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---

# <a name="sfctl-is"></a>sfctl is
查询并向基础结构服务发送命令。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    command| 针对给定基础结构服务实例调用管理命令。|
|    query  | 针对给定基础结构服务实例调用只读查询。|


## <a name="sfctl-is-command"></a>sfctl is command
针对给定基础结构服务实例调用管理命令。

对于配置了一个或多个基础结构服务实例的群集，使用此 API 可向特定基础结构服务实例发送特定于基础结构的命令。 可用命令及其相应的响应格式因运行群集的基础结构而异。 此 API 支持 Service Fabric 平台；它不宜从代码中直接使用。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --command [必需]| 将调用的命令文本。 命令内容特定于基础结构。  默认值：is command。|
| --service-id     | 基础结构服务标识。 这是不包含“fabric:”URI 方案的基础结构服务全名。 只有运行多个基础结构服务实例的群集才需要此参数。|
| --timeout -t     | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug          | 提高日志记录详细程度，显示所有调试日志。|
| --help -h        | 显示此帮助信息并退出。|
| --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query          | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-is-query"></a>sfctl is query
针对给定基础结构服务实例调用只读查询。

对于配置了一个或多个基础结构服务实例的群集，使用此 API 可向特定基础结构服务实例发送特定于基础结构的查询。 可用命令及其相应的响应格式因运行群集的基础结构而异。 此 API 支持 Service Fabric 平台；它不宜从代码中直接使用。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --command [必需]| 将调用的命令文本。 命令内容特定于基础结构。  默认值：is query。|
| --service-id     | 基础结构服务标识。 这是不包含“fabric:”URI 方案的基础结构服务全名。 只有运行多个基础结构服务实例的群集才需要此参数。|
| --timeout -t     | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug          | 提高日志记录详细程度，显示所有调试日志。|
| --help -h        | 显示此帮助信息并退出。|
| --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query          | JMESPath 查询字符串。 有关详细信息，请参阅 http://jmespath.org/。|
| --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [设置](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
