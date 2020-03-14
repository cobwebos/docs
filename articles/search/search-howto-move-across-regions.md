---
title: 如何跨区域移动服务资源
titleSuffix: Azure Cognitive Search
description: 本文介绍如何将 Azure 认知搜索资源从 Azure 云中的一个区域移到另一个区域。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: c31a81d2836e9f8c00dec3c0c2eb3a43800a5322
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136255"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>将 Azure 认知搜索服务移到另一个 Azure 区域

有时，客户会询问将现有搜索服务移到另一个区域。 目前没有内置机制或工具可帮助您处理该任务。 这仍是一个手动过程，本文将对此进行介绍。

> [!NOTE]
> 在 Azure 门户中，所有服务都有一个**导出模板**命令。 对于 Azure 认知搜索，此命令会生成服务（名称、位置、层、副本和分区计数）的基本定义，但不能识别服务的内容，也不会对密钥、角色或日志执行此定义。 尽管该命令存在，但我们不建议使用它来移动搜索服务。

## <a name="steps-for-moving-a-service"></a>移动服务的步骤

如果需要将搜索服务移到不同的区域，则方法应类似于以下步骤：

1. 标识相关服务，了解重定位服务的全部影响。 你可能使用 Azure 存储来记录、知识库或外部数据源。 你可能会将认知服务用于 AI 扩充。 访问其他区域的服务很常见，但会产生额外的带宽费用。 如果使用 AI 扩充，认知服务和 Azure 认知搜索必须位于同一区域。

1. 清点现有服务以获取服务上对象的完整列表。 如果启用了日志记录，请创建并存档历史记录可能需要的任何报表。

1. 检查新区域中的定价和可用性，以确保 Azure 认知搜索以及你可能想要在同一区域中创建的任何相关服务的可用性。 检查功能奇偶校验。 某些预览功能的可用性受到限制。

1. 在新区域中创建服务，然后从源代码中重新发布任何现有索引、索引器、数据源、技能集、知识库和同义词映射。 服务名称必须是唯一的，因此不能重复使用现有名称。

1. 重载索引和知识存储（如果适用）。 你将使用应用程序代码将 JSON 数据推送到索引中，或重新运行索引器从外部源中请求文档。 

1. 启用日志记录，如果使用日志记录，请重新创建安全角色。

1. 更新客户端应用程序和测试套件以使用新的服务名称和 API 密钥，并对所有应用程序进行测试。

1. 在新服务经过完全测试和操作后，删除旧服务。

## <a name="next-steps"></a>后续步骤

+ [选择层](search-sku-tier.md)
+ [创建搜索服务](search-create-service-portal.md)
+ [加载搜索文档](search-what-is-data-import.md)
+ [启用日志记录](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->