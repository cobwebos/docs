---
title: Azure Service Fabric CLI - sfctl compose | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl compose 命令。
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 19afd35248cc0796eddbb50db4f38b813f5d568e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-compose"></a>sfctl compose
创建、删除和管理 Docker Compose 部署。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    create| 通过 Compose 文件部署 Service Fabric 应用程序。|
|    list  | 获取在 Service Fabric 群集中创建的 compose 部署列表。|
|   remove| 从群集中删除现有的 Service Fabric compose 部署。|
|   status| 获取有关 Service Fabric compose 部署的信息。|
|升级       | 开始升级 Service Fabric 群集中的 compose 部署。|
|    upgrade-status| 获取在此 Service Fabric Compose 部署中执行的最新升级的详细信息。|


## <a name="sfctl-compose-create"></a>sfctl compose create
创建 Service Fabric compose 部署。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --file-path [必需]| 目标 Docker Compose 文件的路径。|
 |   --deployment-name [必需]| 部署的名称。|
|    --encrypted-pass             | 不提示输入容器注册表密码，而是使用已加密的通行短语。|
|    --has-pass                   | 提示输入容器注册表的密码。|
|    --timeout -t                 | 服务器超时时间，以秒为单位。  默认值：60。|
 |   --user                       | 用于连接容器注册表的用户名。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-compose-list"></a>sfctl compose list
获取在 Service Fabric 群集中创建的 compose 部署列表。

获取 Service Fabric 群集中已创建的或正在创建的 compose 部署的状态。 响应包括名称、状态和有关 compose 部署的其他详细信息。 如果一页无法容纳这些部署，则返回一页结果及一个继续标记，该标记可用于获取下一页。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --continuation-token| 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。      当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --max-results    | 作为分页查询的一部分返回的最大结果数。      此参数定义返回结果数的上限。      如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页的查询包含返回消息中最多可容纳的结果数。|
| --timeout -t     | 服务器超时时间，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug          | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h        | 显示此帮助消息并退出。|
| --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query          | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-compose-remove"></a>sfctl compose remove
从群集中删除现有的 Service Fabric compose 部署。

删除现有的 Service Fabric compose 部署。 

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --deployment-name [必需]| 部署的标识。 这通常是不带“fabric:”URI 方案的应用程序全名。|
| --timeout -t            | 服务器超时时间，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-compose-status"></a>sfctl compose status
获取有关 Service Fabric compose 部署的信息。

返回 Service Fabric 群集中已创建或正在创建的、且名称与指定为参数的部署相匹配的 compose 部署的状态。 响应包括名称、状态以及有关应用程序的其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --deployment-name [必需]| 部署的标识。 |
| --timeout -t            | 服务器超时时间，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
开始升级 Service Fabric 群集中的 compose 部署。

验证提供的升级参数并开始升级部署。

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|    --file-path        [必需]| 目标 Docker Compose 文件的路径。|
|    --deployment-name [必需]| 部署的名称。|
|    --default-svc-type-health-map| 描述用于评估服务运行状况的运行状况策略的 JSON 编码字典。|
|    --encrypted-pass             | 不提示输入容器注册表密码，而是使用已加密的通行短语。|
 |   --failure-action             | 可能的值包括：“Invalid”、“Rollback”和“Manual”。|
|    --force-restart              | 强制重启。|
 |   --has-pass                   | 提示输入容器注册表的密码。|
|    --health-check-retry         | 运行状况检查重试超时时间，以毫秒为单位。|
|    --health-check-stable        | 运行状况检查稳定持续时间，以毫秒为单位。|
|    --health-check-wait          | 运行状况检查等待持续时间，以毫秒为单位。|
|    --replica-set-check          | 升级副本集检查超时时间，以秒为单位。|
|    --svc-type-health-map        | 描述用于评估不同服务类型的运行状况的运行状况策略的 JSON 编码对象列表。|
|    --timeout -t                 | 服务器超时时间，以秒为单位。  默认值：60。|
|    --unhealthy-app              | 报告错误之前允许的最大不正常应用程序百分比。        例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数。|
|    --upgrade-domain-timeout     | 升级域超时时间，以毫秒为单位。|
|    --upgrade-kind               | 默认值：Rolling。|
|    --upgrade-mode               | 可能的值包括：“Invalid”、“UnmonitoredAuto”、“UnmonitoredManual”和“Monitored”。  默认值：UnmonitoredAuto。|
|    --upgrade-timeout            | 升级超时时间，以毫秒为单位。|
|    --user                       | 用于连接容器注册表的用户名。|
|    --warning-as-error           | 将警告的严重性视为与错误相同。|

### <a name="global-arguments"></a>全局参数
 |参数|说明|
| --- | --- |
|   --debug                      | 提高日志记录详细程度，以显示所有调试日志。|
|    --help -h                    | 显示此帮助消息并退出。|
|   --output -o                  | 输出格式。  允许的值：json、jsonc、table、tsv。 默认值：json。|
|   --query                      | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
|   --verbose                    | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。