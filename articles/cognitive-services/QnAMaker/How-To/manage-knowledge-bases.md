---
title: 管理知识库-QnA Maker
description: QnA Maker 允许您通过提供对知识库设置和内容的访问权限来管理您的知识库。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 08736ce61e6768cfd9ac3d3ded7092a4ab93fca9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551772"
---
# <a name="create-knowledge-base-and-manage-settings"></a>创建知识库并管理设置

QnA Maker 允许您通过提供对知识库设置和数据源的访问权限来管理您的知识库。

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

## <a name="create-a-knowledge-base"></a>创建知识库

1. 使用 Azure 凭据登录到 [QnAMaker.ai](https://QnAMaker.ai) 门户。

1. 在 QnA Maker 门户中，选择“创建知识库”。****

1. 在“创建”页上，如果你已经有 QnA Maker 资源，请跳过“步骤 1”********。

    如果尚未创建此资源，请选择“创建 QnA 服务”****。 此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

    在 Azure 门户中创建资源后，请返回到 QnA Maker 门户，刷新浏览器页，并继续完成“步骤 2”****。

1. 在“步骤 3”**** 中，为在服务中创建的所有知识库选择 Active Directory、订阅、服务（资源）和语言。

   ![选择 QnA Maker 服务知识库的屏幕截图](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 在 **步骤 3**中，命名您的知识库 `My Sample QnA KB` 。

1. 在**步骤 4** 中，按照下表配置设置：

    |设置|值|
    |--|--|
    |**允许从 URL、.pdf 或 .docx 文件进行多轮提取。**|已选中|
    |**默认答案文本**| `Quickstart - default answer not found.`|
    |**+ 添加 URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**聊天内容**|选择“专业”|

1. 在 **步骤 5**中，选择 " **创建知识库**"。

    提取过程需要一些时间来读取文档并确定问题和解答。

    QnA Maker 成功创建知识库后，“知识库”页会打开。**** 可在此页面上编辑知识库的内容。

## <a name="edit-knowledge-base"></a>编辑知识库

1.  在顶部导航栏中选择****“我的知识库”。

       你可以按****“上次修改时间”的降序排序，查看你创建或共享的所有服务。

       ![我的知识库](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 选择特定的知识库以对其进行编辑。

1.  选择“设置”。 以下列表包含可更改的字段。

       |目标|操作|
       |--|--|
       |添加 URL|可以通过单击“管理知识库”- >“+ 添加 URL”**** 链接，添加新的 URL，以便向知识库添加新的常见问题解答内容。|
       |删除 URL|可以通过选择删除图标（垃圾桶）来删除现有 URL。|
       |刷新内容|如果希望知识库抓取现有 URL 的最新内容，请选中“刷新”**** 复选框。 这将更新包含最新 URL 内容一次的知识库。 这不会设置定期更新计划。|
       |添加文件|可以通过选择“管理知识库”**** 并选择“+ 添加文件”**** 将受支持的文件文档添加为知识库的一部分。|
    |导入|您还可以通过选择 " **导入知识库** " 按钮导入任何现有知识库。 |
    |更新|知识库的更新取决于在创建与知识库关联的 QnA Maker 服务时使用的**管理定价层**。 如果需要，还可以从 Azure 门户更新管理层。

  1. 完成对知识库的更改后，请选择页面右上角的 " **保存并训练** "，以便保存更改。

       ![保存和训练](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >如果在选择“保存并训练”**** 前离开页面，则所有更改都将丢失。



## <a name="manage-large-knowledge-bases"></a>管理大型知识库

* **数据源组**： qna 按从中提取它们的数据源进行分组。 你可以展开或折叠数据源。

    ![使用 QnA Maker 数据源栏来折叠和展开数据源问题和解答](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **搜索知识库**：您可以通过在知识库表顶部的文本框中键入来搜索知识库。 单击 Enter 以搜索问题、答案或元数据内容。 单击 X 图标以删除搜索筛选器。

    ![使用问题和解答上方的 QnA Maker 搜索框可将视图缩小为仅筛选匹配项](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **分页**：在数据源中快速移动以管理大型知识库

    ![使用问题和解答上方的 QnA Maker 分页功能可浏览问题和解答页面](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>删除知识库

删除知识库 (KB) 是一项永久性操作， 无法撤消。 删除知识库之前，应该从 QnA Maker 门户的“设置”**** 页导出知识库。

如果与协作者共享知识库，] (collaborate-knowledge-base.md) 然后将其删除，每个人都不会失去访问权限。

## <a name="next-steps"></a>后续步骤

了解如何 [管理](language-knowledge-base.md) 资源中所有知识库的语言。

* 编辑 QnA 对
* 管理 QnA Maker 使用的 Azure 资源