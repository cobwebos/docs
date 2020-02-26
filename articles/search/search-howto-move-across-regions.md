---
title: 如何跨区域移动服务资源
titleSuffix: Azure Cognitive Search
description: 本文介绍如何将 Azure 认知搜索资源从 Azure 云中的一个区域移到另一个区域。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599296"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>将 Azure 认知搜索服务移到另一个 Azure 区域

若要将 Azure 认知服务帐户从一个区域移到另一个区域，你将创建一个导出模板来移动订阅。 移动订阅后，需要移动数据并重新创建服务。

本文将指导如何进行以下操作：

> [!div class="checklist"]
> * 导出模板。
> * 修改模板：添加目标区域、搜索和存储帐户名称。
> * 部署模板以创建新的搜索和存储帐户。
> * 在新区域中验证你的服务状态
> * 清理源区域中的资源。

## <a name="prerequisites"></a>必备条件

- 确保你的帐户使用的服务和功能在目标区域中受支持。

- 对于预览功能，请确保订阅是目标区域的白名单。 有关预览功能的详细信息，请参阅[知识存储](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro)、[增量扩充](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)和[专用终结点](https://docs.microsoft.com/azure/search/service-create-private-endpoint)。

## <a name="assessment-and-planning"></a>评估和规划

将搜索服务移到新区域时，需要将[数据移动到新的存储服务](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account)，然后重新生成索引、技能集和知识存储。 应该记录当前设置并复制 json 文件，以便更轻松、更快速地重新生成服务。

## <a name="moving-your-search-services-resources"></a>移动搜索服务的资源

首先，将导出并修改资源管理器模板。

### <a name="export-a-template"></a>导出模板

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 请参阅资源组页。

> [!div class="mx-imgBorder"]
> ![资源组页面示例](./media/search-move-resource/export-template-sample.png)

3. 选择“所有资源”，

3. 在左侧导航菜单中，选择 "**导出模板**"。

4. 选择 "**导出模板**" 页中的 "**下载**"。

5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。

此 zip 文件包含包含模板的 json 文件和用于部署模板的脚本。

### <a name="modify-the-template"></a>修改模板

你将通过更改搜索和存储帐户名称和区域来修改模板。 名称必须遵循每个服务和区域命名约定的规则。 

若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不带空格的区域名称，"**美国中部** = **centralus**"。

1. 在 Azure 门户中，选择“创建资源”。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。

3. 选择“模板部署”。

4. 选择“创建”。

5. 选择“在编辑器中生成自己的模板”。

6. 选择 "**加载文件**"，然后按照说明加载在上一节中下载并解压缩的**模板 json**文件。

7. 在**模板**文件中，通过设置 "搜索" 和 "存储帐户名称" 的默认值来命名目标搜索和存储帐户。 

8. 将**模板 json**文件中的**location**属性编辑到搜索和存储服务的目标区域。 此示例将目标区域设置为 `centralus`。

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>部署模板

1. 保存**模板 json**文件。

2. 输入或选择属性值：

- **订阅**：选择一个 Azure 订阅。

- **资源组**：选择“新建”，为资源组指定名称。

- **位置**：选择 Azure 位置。

3. 单击 "**我同意上述条款和条件"** 复选框，然后单击 "**选择购买**" 按钮。

## <a name="verifying-your-services-status-in-new-region"></a>在新区域中验证服务状态

若要验证移动，请打开新的资源组，服务将与新区域一起列出。

若要将数据从源区域移动到目标区域，请参阅此文章介绍[如何将数据移动到新的存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)。

## <a name="clean-up-resources-in-your-original-region"></a>清理原始区域中的资源

若要提交更改并完成服务帐户的移动，请删除源服务帐户。

## <a name="next-steps"></a>后续步骤

[创建索引](https://docs.microsoft.com/azure/search/search-get-started-portal)

[创建技能集](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[创建知识库](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

