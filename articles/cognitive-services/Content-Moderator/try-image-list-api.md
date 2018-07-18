---
title: 通过在 Azure 内容审查器中使用自定义列表来审查图像 | Microsoft Docs
description: 在内容审查器 API 控制台中测试运行自定义图像列表。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2d714f017be16d978ffbb877a2b7e78e1caf9169
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365542"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>借助 API 控制台中的自定义列表进行审查

在 Azure 内容审查器中使用[列表管理 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) 创建自定义图像列表。 通过图像审查 API 使用自定义图像列表。 图像审查操作将评估图像。 如果创建自定义列表，该操作还会将其与自定义列表中的图像进行比较。 可使用自定义列表来阻止或允许图像。

> [!NOTE]
> 最大限制为 5 个图像列表，每个列表“不超过 10,000 个图像”。
>

使用列表管理 API 执行以下任务：

- 创建列表。
- 向列表添加图像。
- 针对列表中的图像屏蔽图像。
- 从列表中删除图像。
- 删除列表。
- 编辑列表信息。
- 刷新索引，以便新的扫描中包括对列表的更改。

## <a name="use-the-api-console"></a>使用 API 控制台
需要提供订阅密钥才能在联机控制台中测试运行 API。 密钥位于“设置”选项卡的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="refresh-search-index"></a>刷新搜索索引

对图像列表进行更改后，必须刷新其索引，使更改包括在未来的扫描中。 此步骤类似于桌面上的搜索引擎（如果启用）或 Web 搜索引擎的操作，即不断刷新其索引以包含新文件或页面。

1.  在[图像列表管理 API 引用](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)的左侧菜单中，选择“图像列表”，然后选择“刷新搜索索引”。

  “图像列表 - 刷新搜索索引”页随即打开。

2. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 
 
    ![“图像列表 - 刷新搜索索引”页面上的区域选择](images/test-drive-region.png)

    “图像列表 - 刷新搜索索引”API 控制台随即打开。

3.  在 listId 框中，输入列表 ID。 输入订阅密钥，再选择“发送”。

  ![“图像列表 - 刷新搜索索引”控制台响应内容框](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>创建图像列表

1.  转到[图像列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)。

  “图像列表 - 创建”页随即打开。 

3.  对于“开放 API 测试控制台”，选择与所在位置最相关的区域。

    ![“图像列表 - 创建”页面上的区域选择](images/test-drive-region.png)

    “图像列表 - 创建”API 控制台随即打开。
 
4.  在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

5.  在“请求正文”框中，在“名称”中输入值（例如 MyList），在“说明”中输入相关说明。

  ![“图像列表 - 创建”控制台上的请求正文名称和说明](images/try-terms-list-create-1.png)

6.  使用键值对占位符向列表分配更具描述性的元数据。

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  添加元数据作为键值对，而不是添加实际图像。
 
7.  选择“发送”。 列表已创建。 请注意，ID 值与新的列表相关联。 需在其他图像列表管理功能中使用此 ID。

  ![“图像列表 - 创建”控制台上的“响应”内容框中会显示列表 ID](images/try-terms-list-create-2.png)
 
8.  接下来，向 MyList 添加图像。 在左侧菜单中，选择“图像”，然后选择“添加图像”。

  “图像 - 添加图像”页随即打开。 

9. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。

  ![“图像 - 添加图像”页面上的区域选择](images/test-drive-region.png)

  “图像 - 添加图像”API 控制台随即打开。
 
10. 在 listId 框中输入生成的列表 ID，然后输入要添加的图像的 URL。 输入订阅密钥，再选择“发送”。

11. 要验证已向列表添加图像，请在左侧菜单中选择“图像”，然后选择“获取所有图像 ID”。

  “图像 - 获取所有图像 ID”API 控制台随即打开。
  
12. 在 listId 框中，输入列表 ID，然后输入订阅密钥。 选择“发送”。

  ![“图像 - 获取所有图像 ID”控制台上的“响应”内容框中列出了所输入的图像](images/try-image-list-create-11.png)
 
10. 再添加几个图像。 现在，你已创建自定义图像列表，请使用自定义图像列表尝试[评估图像](try-image-api.md)。 

## <a name="delete-images-and-lists"></a>删除图像和列表

删除图像或列表非常简单。 可使用 API 执行以下任务：

- 删除映像。 （“图像 - 删除”）
- 删除列表中的所有图像而不删除列表。 （“图像 - 删除所有图像”）
- 删除列表及其所有内容。 （“图像列表 - 删除”）

此示例将删除单个图像：

1. 在[图像列表管理 API 引用](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)的左侧菜单中，选择“图像”，然后选择“删除”。 

  “图像 - 删除”页随即打开。

2. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 

  ![“图像 - 删除”页面上的区域选择](images/test-drive-region.png)
 
  “图像 - 删除”API 控制台随即打开。
 
3.  在 listId 框中，输入要从中删除图像的列表 ID。  这是对 MyList 在“图像 - 获取所有图像 ID”控制台中返回的数字。 然后，输入要删除图像的 ImageId。 

在本示例中，列表 ID 为 58953，是 ContentSource 的值。 图像 ID 是 59021，是 ContentIds 的值。

4.  输入订阅密钥，再选择“发送”。

5.  要验证已删除图像，请使用“图像 - 获取所有图像 ID”控制台。
 
## <a name="change-list-information"></a>更改列表信息

可以编辑列表的名称和说明，并添加元数据项。

1.  在[图像列表管理 API 引用](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)的左侧菜单中，选择“图像列表”，然后选择“更新详细信息”。 

  “图像列表 - 更新详细信息”页随即打开。

2. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。  

    ![“图像列表 - 更新详细信息”页面上的区域选择](images/test-drive-region.png)

    “图像列表 - 更新详细信息”API 控制台随即打开。
 
3.  在 listId 框中，输入列表 ID，然后输入订阅密钥。

4.  在“请求正文”框中，进行编辑，然后选择页面上的“发送”按钮。

  ![“图像列表 - 更新详细信息”控制台上的请求正文编辑](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>后续步骤

要与应用程序集成，请在代码中使用 REST API，或通过[图像列表 .NET 快速入门](image-lists-quickstart-dotnet.md)开始。
