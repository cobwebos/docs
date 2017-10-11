---
title: "Azure Monitor CLI 1.0 快速入门示例。 | Microsoft 文档"
description: "Azure Monitor 功能的示例 CLI 1.0 命令。 Azure 监视器是一项 Microsoft Azure 服务，允许用户发送警报通知、基于所配置的遥测数据调用 Web URL 和自动缩放云服务、虚拟机和 Web 应用。"
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Azure Monitor 跨平台 CLI 1.0 快速入门示例
本文演示有助于访问 Azure 监视器功能的示例命令行接口 (CLI) 命令。 使用 Azure 监视器，可以基于配置的遥测数据值自动缩放云服务、虚拟机和 Web 应用以及发送警报通知或调用 Web URL。

> [!NOTE]
> 自 2016 年 9 月 25 日起，“Azure Insights”更名为 Azure 监视器。 但是，命名空间及其下命令仍包含“insights”。
> 
> 

## <a name="prerequisites"></a>先决条件
如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](../cli-install-nodejs.md)。 如果不熟悉 Azure CLI，可在[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure Resource Manager 配合使用](../xplat-cli-azure-resource-manager.md)中了解相关详细信息。

在 Windows 中，从 [Node.js 网站](https://nodejs.org/)安装 npm。 完成安装之后，通过“以管理员身份运行”权限使用 CMD.exe，从安装 npm 的文件夹执行以下命令：

```console
npm install azure-cli --global
```

接下来，导航到所需的任何文件夹/位置并在命令行处输入：

```console
azure help
```

## <a name="log-in-to-azure"></a>登录 Azure
第一步是登录 Azure 帐户。

```console
azure login
```

运行此命令后，必须按照屏幕上的说明进行登录。 之后，将看到帐户、TenantId 和默认订阅 ID。所有命令都在默认订阅的上下文中工作。

若要列出当前订阅的详细信息，请使用以下命令。

```console
azure account show
```

要将工作上下文更改为其他订阅，请使用以下命令。

```console
azure account set "subscription ID or subscription name"
```

若要使用 Azure Resource Manager 和 Azure 监视器命令，需要处于 Azure Resource Manager 模式下。

```console
azure config mode arm
```

若要查看所有受支持的 Azure 监视器命令的列表，请执行以下操作。

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>查看订阅的活动日志
若要查看活动日志事件的列表，请执行以下操作。

```console
azure insights logs list [options]
```

尝试以下操作以查看所有可用选项。

```console
azure insights logs list -help
```

下面是一个按 resourceGroup 列出日志的示例

```console
azure insights logs list --resourceGroup "myrg1"
```

按调用方列出日志的示例

```console
azure insights logs list --caller "myname@company.com"
```

在开始和结束日期内，按调用方对资源类型列出日志的示例

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>使用警报
可以按照该部分中的信息使用警报。

### <a name="get-alert-rules-in-a-resource-group"></a>获取资源组中的警报规则
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>创建指标警报规则
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>创建 webtest 警报规则
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>删除警报规则
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>日志配置文件
可以按照此部分中的信息使用日志配置文件。

### <a name="get-a-log-profile"></a>获取日志配置文件
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>添加没有保留期的日志配置文件
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>删除日志配置文件
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>添加具有保留期的日志配置文件
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>添加具有保留期和 EventHub 的日志配置文件
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>诊断
可以按照此部分中的信息使用诊断设置。

### <a name="get-a-diagnostic-setting"></a>获取诊断设置
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>禁用诊断设置
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>启用没有保留期的诊断设置
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>自动缩放
按照此部分中的信息使用自动缩放设置。 需要修改这些示例。

### <a name="get-autoscale-settings-for-a-resource-group"></a>获取资源组的自动缩放设置
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>按名称获取资源组的自动缩放设置
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>设置自动缩放设置
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
