---
title: 将资源清单、使用情况数据、指标和日志上传到 Azure Monitor
description: 将资源清单、使用情况数据、指标和日志上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934317"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>将资源清单、使用情况数据、指标和日志上传到 Azure Monitor

利用 Azure Arc 数据服务，可以 *选择* 将指标和日志上传到 Azure Monitor，以便可以聚合和分析指标、日志、引发警报、发送通知或触发自动操作。 通过将数据发送到 Azure Monitor，还可以将监视和记录数据存储在站点以外的位置，还可以大规模地存储数据，以便进行高级分析。  如果有多个包含 Azure Arc 数据服务的站点，则可以使用 Azure Monitor 作为中心位置，收集整个站点中的所有日志和指标。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>开始之前

启用日志和指标上传方案需要几个一次性的安装步骤：

1) 创建一个服务主体/Azure Active Directory 应用程序，包括创建客户端访问机密并将服务主体分配给订阅上的 "监视指标发布者" 角色， (s) 数据库实例资源所在的位置。
2) 创建 log analytics 工作区并获取密钥并在环境变量中设置信息。

第一项是上传指标所必需的，第二项是上传日志所需的。

按照以下命令创建指标上传服务主体，并将其分配给 "监视指标发布者" 和 "参与者" 角色，以便服务主体可以上传度量值并执行创建和上载操作。

## <a name="create-service-principal-and-assign-roles"></a>创建服务主体并分配角色

按照以下命令创建指标上传服务主体，并将其分配给 "监视指标发布者" 角色：

若要创建服务主体，请运行以下命令：

> [!NOTE]
> 若要创建服务主体，需要 [在 Azure 中具有特定权限](/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

示例输出：

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

将 appId 和租户值保存在环境变量中供以后使用：

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

运行此命令，将服务主体分配到数据库实例资源所在的订阅上的 "监视指标发布者" 角色：

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

示例输出：

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>创建 log analytics 工作区

接下来，执行以下命令以创建 Log Analytics 工作区，并将访问信息设置为环境变量。

> [!NOTE]
> 如果你已有工作区，请跳过此步骤。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

示例输出：

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>为环境变量分配 ID 和共享密钥

将 customerId (工作区 ID 保存) 为稍后要使用的环境变量：

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

此命令将打印连接到 log analytics 工作区所需的访问密钥：

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

示例输出：

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

将主键保存在以后要使用的环境变量中：

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>设置最终环境变量和确认

在环境变量中设置 SPN 授权 URL：

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

如果需要，请检查以确保设置所需的所有环境变量：

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>将指标上载到 Azure Monitor

若要上载 Azure SQL 托管实例和 Azure Database for PostgreSQL 超大规模服务器组的指标，请运行以下 CLI 命令：

这会将所有指标导出到指定文件：

```console
azdata arc dc export -t metrics --path metrics.json
```

这会将指标上传到 Azure Monitor：

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>在门户中查看指标

上传指标后，你应该能够从 Azure 门户将其可视化。

若要在门户中查看度量值，请使用此特殊链接打开门户： <https://portal.azure.com> 然后在搜索栏中按名称搜索数据库实例：

你可以在 "概述" 页上查看 CPU 利用率，或者如果想要更详细的指标，可以单击左侧导航面板中的 "指标"

选择 sql server 作为指标命名空间：

选择要可视化的指标 (也可以选择多个) ：

将频率更改为最近30分钟：

> [!NOTE]
> 只能上载过去30分钟的指标。 Azure Monitor 拒绝超过30分钟的指标。

## <a name="upload-logs-to-azure-monitor"></a>将日志上传到 Azure Monitor

 若要上载 Azure SQL 托管实例和 Azure Database for PostgreSQL 超大规模服务器组的日志，请运行以下 CLI 命令-

这会将所有日志导出到指定文件：

```console
azdata arc dc export -t logs --path logs.json
```

这会将日志上传到 Azure monitor log analytics 工作区：

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>查看 Azure 门户中的日志

上传日志后，你应该能够使用日志查询资源管理器对其进行查询，如下所示：

1. 打开 Azure 门户，然后在顶部搜索栏中按名称搜索你的工作区，然后选择它
2. 单击左侧面板中的“日志”
3. 单击 "开始 (" 或单击 "入门" 页上的链接以了解有关 Log Analytics 的详细信息) 
4. 如果是第一次，请按照教程详细了解 Log Analytics
5. 展开表列表底部的“自定义日志”，你将看到一个名为“sql_instance_logs_CL”的表。
6. 单击表名称旁边的“眼睛”图标
7. 单击“在查询编辑器中查看”按钮
8. 现在，查询编辑器中将有一个查询，它将显示日志中最近的 10 个事件
9. 在这里，可以使用查询编辑器、设置警报等来尝试查询日志。

## <a name="automating-metrics-and-logs-uploads-optional"></a>自动化指标和日志上传 (可选) 

如果要不断上传指标和日志，可以创建一个脚本，并每隔几分钟在计时器上运行它。  下面是使用 Linux shell 脚本自动执行上载的示例。

在 "常用文本/代码编辑器" 中，将以下内容添加到文件中，并将其另存为脚本可执行文件（如 sh (Linux/Mac) 或 .cmd，.bat，. ps1）。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

使脚本文件可执行

```console
chmod +x myuploadscript.sh
```

每隔2分钟运行一次脚本：

```console
watch -n 120 ./myuploadscript.sh
```

你还可以使用诸如 cron 或 Windows 任务计划程序的作业计划程序或 Ansible、Puppet 或 Chef 等 orchestrator。
