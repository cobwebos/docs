---
title: 通过 Azure 内容审查器中的自定义术语列表审查文本 | Microsoft Docs
description: 在内容审查器 API 控制台中试运行自定义术语列表。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2542e4590781879408aafe8d072eceef157e02c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365539"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>在 API 控制台中使用自定义术语列表进行审查

Azure 内容审查器中的默认全局术语列表足以满足大多数内容审查需求。 但是，你可能需要筛选组织特定的术语。 例如，可能需要标记竞争对手的名称作进一步审查。 

使用[列表管理 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)来创建要供文本审查 API 使用的自定义术语列表。 “文本 - 筛选”操作扫描文本中是否存在侮辱性内容，并将文本与自定义黑名单和共享黑名单进行对比。

> [!NOTE]
> 最多只能使用 5 个术语列表，每个列表不超过 10,000 条术语。
>

可使用列表管理 API 执行以下任务：
- 创建列表。
- 向列表添加术语。
- 针对列表中的术语筛选术语。
- 从列表中删除术语。
- 删除列表。
- 编辑列表信息。
- 筛选索引，使新的扫描中包含列表更改项。

## <a name="use-the-api-console"></a>使用 API 控制台

需要提供订阅密钥才能在联机控制台中试运行 API。 此密钥位于“设置”选项卡的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="refresh-search-index"></a>刷新搜索索引

更改术语列表后，必须筛选其索引，这样后续扫描才包含所作更改。 此步骤类似于桌面上的搜索引擎（如果启用）或 Web 搜索引擎的操作，即不断刷新其索引以包含新文件或页面。

1.  在[术语列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)的左侧菜单中，选择“术语列表”，然后选择“刷新搜索索引”。 

  “术语列表 - 刷新搜索索引”页随即打开。

2. 对于“开放 API 测试控制台”，选择最能描述你所在位置的区域。 

  ![“术语列表 - 刷新搜索索引”页面上的区域选择](images/test-drive-region.png)

  “术语列表 - 刷新搜索索引”API 控制台随即打开。

3.  在 listId 框中，输入列表 ID。 输入订阅密钥，再选择“发送”。

  ![“术语列表 API - 刷新搜索索引”控制台响应内容框](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>创建术语列表
1.  转到[术语列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)。 

  “术语列表 - 创建”页随即打开。

2.  对于“开放 API 测试控制台”，选择最能描述你所在位置的区域。 

  ![“术语列表 - 创建”页面上的区域选择](images/test-drive-region.png)

  “术语列表 - 创建”API 控制台随即打开。
 
3.  在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

4.  在“请求正文”框中，输入 Name 的值（例如 MyList）和说明。

  ![“术语列表 - 创建”控制台上的请求正文名称和说明](images/try-terms-list-create-1.png)

5.  使用键/值对占位符向列表分配更具描述性的元数据。

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  添加列表元数据作为键/值对，而不是添加实际术语。
 
6.  选择“发送”。 列表已创建。 请注意，ID 值与新的列表相关联。 需在其他术语列表管理功能中使用此 ID。

  ![“术语列表 - 创建”控制台上的响应内容框中显示列表 ID](images/try-terms-list-create-2.png)
 
7.  向 MyList 添加术语。 在左侧菜单的“术语”下，选择“添加术语”。 

  “术语 - 添加术语”页面随即打开。 

8.  对于“开放 API 测试控制台”，选择最能描述你所在位置的区域。 

  ![“术语 - 添加术语”页面上的区域选择](images/test-drive-region.png)

  “术语 - 添加术语”API 控制台随即打开。
 
9.  在 listId 框中，输入所生成的列表 ID，再选择语言的值。 输入订阅密钥，再选择“发送”。

  ![“术语 - 添加术语”控制台查询参数](images/try-terms-list-create-3.png)
 
10. 要验证确保已向列表添加术语，请在左侧菜单中选择“术语”，然后选择“获取所有术语”。 

  “术语 - 获取所有术语”API 控制台随即打开。

11. 在 listId 框中，输入列表 ID，然后输入订阅密钥。 选择“发送”。

12. 在“响应内容”框中，验证所输入的术语。

  ![“术语 - 获取所有术语”控制台上的“响应内容”框中列出了所输入的术语](images/try-terms-list-create-4.png)
 
13. 再添加几条术语。 现在，你已创建了自定义术语列表，接着请尝试使用自定义术语列表[扫描一些文本](try-text-api.md)。 

## <a name="delete-terms-and-lists"></a>删除术语和列表

删除术语或列表非常简单。 使用 API 执行以下任务：

- 删除术语。 （术语 - 删除）
- 删除列表中的所有术语而不删除列表。 （术语 - 删除所有术语）
- 删除列表及其所有内容。 （术语列表 - 删除）

下例删除一个术语。

1.  在[术语列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)的左侧菜单中，选择“术语”，然后选择“删除”。 

  “术语 - 删除”随即打开。

2. 对于“开放 API 测试控制台”，选择最能描述你所在位置的区域。 

  ![“术语 - 删除”页面上的区域选择](images/test-drive-region.png)

  “术语 - 删除”API 控制台随即打开。
  
3.  在 listId 框中，输入要从中删除术语的列表的 ID。 此 ID 是一个数字（本例中为 122），它是“术语列表 - 获取详细信息”控制台中针对 MyList 返回的内容。 输入术语，再选择一种语言。
 
  ![“术语 - 删除”控制台查询参数](images/try-terms-list-delete-1.png)

4.  输入订阅密钥，再选择“发送”。

5.  要验证术语是否已删除，请使用“术语列表 - 获取所有术语”控制台。

  ![“术语列表 - 获取所有术语”控制台中的“响应内容”框显示术语已删除](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>更改列表信息

可编辑列表的名称和说明，还可添加元数据项。

1.  在[术语列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)的左侧菜单中，选择“术语列表”，然后选择“更新详细信息”。 

  “术语列表 - 更新详细信息”页随即打开。

2. 对于“开放 API 测试控制台”，选择最能描述你所在位置的区域。 

  ![“术语列表 - 更新详细信息”页上的区域选择](images/test-drive-region.png)

  “术语列表 - 更新详细信息”API 控制台随即打开。

3.  在 listId 框中，输入列表 ID，然后输入订阅密钥。

4.  在“请求正文”框中进行编辑，然后选择“发送”。

  ![“术语列表 - 更新详细信息”控制台上的请求正文编辑](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>后续步骤

要与应用程序集成，请在代码中使用 REST API，或通过[术语列表 .NET 快速入门](term-lists-quickstart-dotnet.md)开始。
