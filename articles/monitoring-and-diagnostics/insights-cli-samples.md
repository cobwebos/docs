---
title: Azure Monitor CLI 快速入门示例
description: Azure Monitor 功能的示例 CLI 2.0 命令。 Azure 监视器是一项 Microsoft Azure 服务，允许用户发送警报通知、基于所配置的遥测数据调用 Web URL 和自动缩放云服务、虚拟机和 Web 应用。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: dfc9a65325d25889c9d8d3c10aa0dcda2b5a9980
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213198"
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Azure Monitor CLI 2.0 快速入门示例
本文演示有助于访问 Azure 监视器功能的示例命令行接口 (CLI) 命令。 使用 Azure 监视器，可以基于配置的遥测数据值自动缩放云服务、虚拟机和 Web 应用以及发送警报通知或调用 Web URL。

## <a name="prerequisites"></a>先决条件

如果尚未安装 Azure CLI，请按照[安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 的说明进行操作。 还可以使用 [Azure Cloud Shell](/azure/cloud-shell) 在浏览器中以交互式体验运行 CLI。 请参阅 [Azure Monitor CLI 参考](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)中的所有可用命令的完整参考。 

## <a name="log-in-to-azure"></a>登录 Azure
第一步是登录 Azure 帐户。

```azurecli
az login
```

运行此命令后，必须按照屏幕上的说明进行登录。 所有命令都在默认订阅的上下文中工作。

若要列出当前订阅的详细信息，请使用以下命令。

```azurecli
az account show
```

要将工作上下文更改为其他订阅，请使用以下命令。

```azurecli
az account set -s <Subscription ID or name>
```

若要查看所有受支持的 Azure 监视器命令的列表，请执行以下操作。

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>查看订阅的活动日志

若要查看活动日志事件的列表，请执行以下操作。

```azurecli
az monitor activity-log list
```

尝试以下操作以查看所有可用选项。

```azurecli
az monitor activity-log list -h
```

下面是一个按 resourceGroup 列出日志的示例

```azurecli
az monitor activity-log list --resource-group <group name>
```

按调用方列出日志的示例

```azurecli
az monitor activity-log list --caller myname@company.com
```

在日期范围内，按调用方对资源类型列出日志的示例

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>使用警报 
> [!NOTE]
> 目前，仅在 CLI 中支持警报（经典）。 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>在资源组中获取警报（经典）规则

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>创建指标警报（经典）规则

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>删除警报（经典）规则

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>日志配置文件

可以按照此部分中的信息使用日志配置文件。

### <a name="get-a-log-profile"></a>获取日志配置文件

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>添加具有保留期的日志配置文件

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>添加具有保留期和 EventHub 的日志配置文件

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>删除日志配置文件

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>诊断

可以按照此部分中的信息使用诊断设置。

### <a name="get-a-diagnostic-setting"></a>获取诊断设置

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>创建诊断日志设置 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>删除诊断设置

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>自动缩放

按照此部分中的信息使用自动缩放设置。 需要修改这些示例。

### <a name="get-autoscale-settings-for-a-resource-group"></a>获取资源组的自动缩放设置

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>按名称获取资源组的自动缩放设置

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>设置自动缩放设置

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
