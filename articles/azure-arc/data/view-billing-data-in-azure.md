---
title: 将计费数据上传到 Azure 并在 Azure 门户中查看
description: 将计费数据上传到 Azure 并在 Azure 门户中查看
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 55269b45159210eec2ec7a6dd8eaea661ff13ebd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760300"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>将计费数据上传到 Azure 并在 Azure 门户中查看

> [!IMPORTANT] 
>  预览期间使用启用了 Azure Arc 的数据服务不会产生费用。 尽管计费系统的工作结束，但计费计量器设置为 $0。  如果遵循此方案，你将在你的帐单中看到一个当前命名为**混合数据服务**的服务以及一个名为 microsoft 的类型的资源的条目 **。AzureData/ `<resource type>` **。 你将能够看到你创建的每个数据服务的记录-Azure Arc，但每个记录将按 $0 计费。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>连接模式-计费数据的含意

将来，可以通过两种模式运行启用了 Azure Arc 的数据服务：

- **间接连接** -不会直接连接到 Azure。 数据仅通过导出/上载过程发送到 Azure。 目前，所有 Azure Arc 数据服务部署在此模式下均以预览版的方式运行。
- **直接连接** -在此模式下，将依赖于支持 azure Arc 的 Kubernetes 服务，以便在 azure 与启用了 azure arc 的数据服务的 Kubernetes 群集之间提供直接连接。 这可以实现更多功能，还可让你使用 Azure 门户和 Azure CLI 管理启用了 Azure Arc 的数据服务，就像在 Azure PaaS 中管理数据服务一样。  此连接模式目前尚不提供预览版，但即将推出。

可以阅读有关 [连接模式](https://docs.microsoft.com/azure/azure-arc/data/connectivity)之间差异的详细信息。

在间接连接模式下，计费数据会定期从 Azure Arc 数据控制器输出到安全文件，然后上传到 Azure 并进行处理。  在即将到来的直接连接模式下，计费数据会自动发送到 Azure 大约1小时，以便对服务成本进行近乎实时的查看。 在间接连接模式下导出和上传数据的过程也可以使用脚本自动进行，我们可能会构建一个可为你执行此操作的服务。

## <a name="upload-billing-data-to-azure"></a>将计费数据上传到 Azure

若要将帐单数据上传到 Azure，应首先执行以下操作：

1. 如果还没有启用了 Azure Arc 的数据服务，请创建一个。 例如，创建以下项之一：
   - [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)
   - [创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)
1. [将资源清单、使用情况数据、指标和日志上传到 Azure Monitor （](upload-metrics-and-logs-to-azure-monitor.md) 如果尚未这样做）。
1. 等待至少2小时后，创建数据服务，以便计费遥测收集过程可以收集一些计费数据。

运行以下命令以导出帐单数据：

```console
azdata arc dc export -t usage -p usage.json
```

目前，文件未加密，因此可以查看内容。 可以随意在文本编辑器中打开，并查看内容的外观。

你会注意到有两个数据集： `resources` 和 `data` 。 `resources`是数据控制器、PostgreSQL 超大规模服务器组和 SQL 托管实例。 `resources`数据中的记录将捕获资源历史记录中的相关事件-创建时间、更新时间以及删除时间。 `data`记录将捕获给定实例每小时可使用的核心数。

条目的示例 `resource` ：

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

条目的示例 `data` ：

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

运行以下命令，将文件中的 usage.js上传到 Azure：

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>查看 Azure 门户中的计费数据

请按照以下步骤在 Azure 门户中查看计费数据：

1. 使用特殊 URL 打开 Azure 门户：  [https://aka.ms/arcdata](https://aka.ms/arcdata) 。
1. 在 " **成本管理** " 中屏幕类型顶部的搜索框中，单击 "成本管理" 服务。
1. 单击左侧的 " **成本分析** " 选项卡。
1. 单击视图顶部的 " **按资源计算成本** " 按钮。
1. 确保你的作用域设置为在其中创建了数据服务资源的订阅。
1. 在视图顶部附近的 "范围" 选择器旁的 "视图" 下拉菜单中选择 " **按资源成本** "。
1. 请确保日期筛选器设置为 **本月** ，或在给定创建数据服务资源的时间时有意义的其他时间范围。
1. **Add filter** **Resource type**  =  `microsoft.azuredata/<data service type>` 如果要仅筛选到一种类型的支持 Azure Arc 的数据服务，请单击 "添加筛选器" 以按资源类型添加筛选器。
1. 现在，你将看到已创建并上载到 Azure 的所有资源的列表。 由于计费指标为 $0，你会看到成本始终为 $0。

## <a name="download-billing-data"></a>下载计费数据

您可以直接从 Azure 门户下载帐单汇总数据。

1. 按照上述说明，在 " **按资源类型查看 > 查看** " 视图中，单击顶部附近的 "下载" 按钮。
1. 选择下载文件类型-Excel 或 CSV，并单击 " **下载数据** " 按钮。
1. 根据所选的文件类型，在适当的编辑器中打开文件。

## <a name="export-billing-data"></a>导出帐单数据

你还可以通过创建计费导出作业来定期自动将 **详细** 的使用情况和计费数据导出到 Azure 存储容器。 如果希望查看计费的详细信息（例如，计费期间内某个给定实例按多少小时计费），这会很有用。

请按照以下步骤设置计费导出作业：

1. 单击左侧的 "导出"。
1. 单击“添加”。
1. 输入名称和导出频率，并单击 "下一步"。
1. 选择创建新的存储帐户或创建一个新的存储帐户，并填写表单以指定要将帐单数据文件导出到的存储帐户、容器和目录路径，然后单击 "下一步"。
1. 单击“创建”。

计费数据导出文件将在大约4小时内可用，并将按照创建计费导出作业时指定的计划导出。

按照以下步骤查看导出的计费数据文件：

可以在 Azure 门户中验证帐单数据文件。 

> [!IMPORTANT]
> 创建计费导出作业后，请等待4小时，然后继续执行以下步骤。

1. 在门户顶部的 "搜索" 框中，键入 **存储帐户** ，并单击 " **存储帐户**"。
3. 单击在上面创建计费导出作业时指定的存储帐户。
4. 单击左侧的 "容器"。
5. 单击上面创建计费导出作业时指定的容器。
6. 单击上面创建计费导出作业时指定的文件夹。
7. 向下钻取生成的文件夹和文件，然后单击其中一个生成的 .csv 文件。
8. 单击 "下载" 按钮，该按钮会将文件保存到本地下载文件夹。
9. 使用 .csv 文件查看器（如 Excel）打开该文件。
10. 筛选结果以仅显示**资源类型**为的行  =  `Microsoft.AzureData/<data service resource type` 。
11. 你将在 UsageQuantity 列中看到当前24小时内使用实例的小时数。
