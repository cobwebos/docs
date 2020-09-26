---
title: 将使用情况数据、指标和日志上传到 Azure Monitor
description: 将资源清单、使用情况数据、指标和日志上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7c8e92604cc6188d17411a266f8b27db55c8fbad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317270"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>将使用情况数据、指标和日志上传到 Azure Monitor

监视是 Azure Arc 启用的数据服务提供的众多内置功能之一。 

## <a name="upload-usage-data"></a>上载使用情况数据

可以通过以下两个步骤将使用情况信息（如清单和资源使用情况）上传到 Azure：

1. 使用命令导出使用情况数据 ```azdata export``` ，如下所示：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   此命令创建一个 `usage.json` 文件，其中包含所有启用了 Azure Arc 的数据资源，如 SQL 托管实例和在数据控制器上创建的 PostgreSQL 超大规模实例等。

2. 使用命令上载使用情况数据 ```azdata upload```

   > [!NOTE]
   > 请在创建 Azure Arc 数据控制器之前等待至少24小时，然后再运行上传

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>上传指标和日志

利用 Azure Arc 数据服务，您可以选择将指标和日志上传到 Azure Monitor，以便可以聚合和分析指标、日志、引发警报、发送通知或触发自动操作。 

通过将数据发送到 Azure Monitor，还可以将监视和记录数据存储在异地，并以大规模的规模存储数据，从而实现数据的长期存储以实现高级分析。

如果有多个具有 Azure Arc 数据服务的站点，则可以使用 Azure Monitor 作为中心位置，收集整个站点中的所有日志和指标。

### <a name="before-you-begin"></a>开始之前

启用日志和指标上传方案需要几个一次性的安装步骤：

1. 创建一个服务主体/Azure Active Directory 应用程序，包括创建客户端访问机密并将服务主体分配给订阅上的 "监视指标发布者" 角色， (s) 数据库实例资源所在的位置。
2. 创建 log analytics 工作区并获取密钥并在环境变量中设置信息。

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

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

将 appId 和租户值保存在环境变量中供以后使用。 

若要通过 PowerShell 保存 appId 和租户值，请按照以下示例操作：

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

或者，在 Linux 或 macOS 上，可以通过以下示例保存 appId 和租户值：

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

运行此命令，将服务主体分配到数据库实例资源所在的订阅上的 "监视指标发布者" 角色：


> [!NOTE]
> 在 Windows 环境中运行时，你需要为角色名称使用双引号。


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

示例输出：

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>创建 log analytics 工作区

接下来，执行以下命令以创建 Log Analytics 工作区，并将访问信息设置为环境变量。

> [!NOTE]
> 如果你已有工作区，请跳过此步骤。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

示例输出：

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

若要上载启用了 Azure arc 的 SQL 托管实例和启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的指标，请运行以下 CLI 命令：

1. 将所有指标导出到指定的文件：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. 将指标上传到 Azure monitor：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >在为第一次上载创建 Azure Arc 启用的数据实例之后等待至少30分钟
   >
   >请确保 `upload` 此后的指标 `export` Azure Monitor 仅接受过去30分钟的指标。 [了解详细信息](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


如果在导出过程中看到指示 "无法获取指标" 的任何错误，请 ```true``` 通过运行以下命令来检查数据收集是否设置为：

```console
azdata arc dc config show
```

并在 "安全" 部分下查找

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

验证 `allowNodeMetricsCollection` 和属性是否 `allowPodMetricsCollection` 设置为 `true` 。

## <a name="view-the-metrics-in-the-portal"></a>在门户中查看指标

上传度量值后，可以从 Azure 门户查看它们。
> [!NOTE]
> 请注意，可能需要几分钟时间来处理上载的数据，然后才能在门户中查看度量值。


若要在门户中查看度量值，请使用此链接打开门户： <https://portal.azure.com> 然后在搜索栏中按名称搜索数据库实例：

你可以在 "概述" 页上查看 CPU 利用率，或者如果想要更详细的指标，可以单击左侧导航面板中的 "指标"

选择 sql server 作为指标命名空间：

选择要可视化的指标 (也可以选择多个) ：

将频率更改为最近30分钟：

> [!NOTE]
> 只能上载过去30分钟的指标。 Azure Monitor 拒绝超过30分钟的指标。

## <a name="upload-logs-to-azure-monitor"></a>将日志上传到 Azure Monitor

 若要上载启用了 Azure Arc 的 SQL 托管实例和 AzureArc 的日志，请运行以下 CLI 命令：

1. 将所有日志导出到指定的文件：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. 将日志上传到 Azure monitor log analytics 工作区：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>查看 Azure 门户中的日志

上传日志后，你应该能够使用日志查询资源管理器对其进行查询，如下所示：

1. 打开 Azure 门户，然后在顶部搜索栏中按名称搜索你的工作区，然后选择它
2. 单击左侧面板中的“日志”
3. 单击 "开始 (" 或单击 "入门" 页上的链接以了解有关 Log Analytics 的详细信息) 
4. 如果你是首次使用，请按照教程了解 Log Analytics 的详细信息 Log Analytics
5. 展开表列表底部的“自定义日志”，你将看到一个名为“sql_instance_logs_CL”的表。
6. 单击表名称旁边的“眼睛”图标
7. 单击“在查询编辑器中查看”按钮
8. 现在，你将在查询编辑器中创建一个查询，用于在日志中显示最新的10个事件
9. 在这里，可以使用查询编辑器、设置警报等来尝试查询日志。

## <a name="automating-uploads-optional"></a>自动上载 (可选) 

如果要按计划上载指标和日志，可以创建一个脚本，并每隔几分钟在计时器上运行它。 下面是使用 Linux shell 脚本自动执行上载的示例。

在常用文本/代码编辑器中，将以下脚本添加到该文件中，并将其另存为脚本可执行文件，例如 (Linux/Mac) 或 .cmd，.bat，. ps1。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

使脚本文件可执行

```console
chmod +x myuploadscript.sh
```

每隔20分钟运行一次脚本：

```console
watch -n 1200 ./myuploadscript.sh
```

你还可以使用诸如 cron 或 Windows 任务计划程序的作业计划程序或 Ansible、Puppet 或 Chef 等 orchestrator。

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>有关导出和上载使用情况的一般指南，指标

在启用了 Azure Arc 的数据服务上创建、读取、更新和删除 (CRUD) 操作，以便进行计费和监视。 存在监视这些 CRUD 操作并相应计算消耗的后台服务。 实际使用情况或消耗计算按计划进行，并在后台完成。 

在预览期间，此过程在夜间发生。 一般原则是每天仅上载一次使用。 当在同一个24小时内将使用情况信息导出并上传多次时，只 Azure 门户中的资源清单进行更新，而不会更新资源使用情况。

对于上传指标，Azure monitor 只接受过去30分钟的数据 ([了解更多](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)) 。 上传指标的指导是在创建导出文件后立即上载指标，以便查看 Azure 门户中的整个数据集。 例如，如果在 2:00 PM 导出指标，并在 2:50 PM 运行上传命令。 由于 Azure Monitor 仅接受过去30分钟的数据，因此你可能在门户中看不到任何数据。 

## <a name="next-steps"></a>后续步骤

[将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md)

[查看 Azure 门户中的 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
