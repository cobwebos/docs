---
title: Azure 和 Dynamcis 365 咨询服务套餐 | Microsoft Docs
description: 有关在云合作伙伴门户中定义和发布 Azure 或 Dynamcis 365 咨询服务套餐的指导。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310413"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Azure 和 Dynamics 365 咨询服务套餐

<table> <tr> <td>本部分介绍如何将咨询服务发布到 Microsoft <a href="https://azuremarketplace.microsoft.com">Azure 市场</a>或 <a href="https://appsource.microsoft.com">AppSource 市场</a>。 基于 Microsoft <a href="https://dynamics.microsoft.com">Dynamics 365</a>、<a href="https://products.office.com">Office 365</a>、<a href="https://powerbi.microsoft.com">Power BI</a> 和 <a href="https://powerapps.microsoft.com">PowerApps</a> 的解决方案有资格在 AppSource 中列出。 基于其他 Microsoft <a href="https://azure.microsoft.com/services">Azure 服务</a>的其他套餐有资格在 Azure 市场中列出。 </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>发布的好处

发布到上述任一 Microsoft 市场可以获得巨大的好处：

- 利用 Microsoft 品牌推广你的公司。
- 有机会与 AppSource 中的 1 亿多个 Office 365 和 Dynamics 365 用户接触；通过 Azure 市场与 20 多万家组织接触。
- 从这些市场获得优质的潜在顾客。
- 通过 Microsoft 现场和远程销售团队推广你的服务。


## <a name="define-your-consulting-services-offer"></a>定义咨询服务套餐

定义打包的咨询服务套餐。 侧重于提供给单个客户的固定范围、固定持续时间、估算价格、固定价格（或免费），以及主要面向售前服务的套餐。 选择热门的且能有效促成新业务的可重复打包互动功能。

## <a name="publish-a-consulting-service-offer"></a>发布咨询服务套餐

以下部分介绍发布咨询服务套餐的过程

1.  创建新套餐
2.  定义套餐设置
3.  输入店面详细信息，以及是要在 Azure 市场还是 AppSource 中发布。
4.  发布套餐

### <a name="create-a-new-offer"></a>创建新套餐

若要创建新套餐，请完成以下步骤：

1.  在云合作伙伴门户的主菜单中，选择“新建套餐”。
2.  在“新建套餐”菜单中，选择“咨询服务”。

    ![创建新的咨询服务套餐](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>定义套餐设置

在“新建套餐”屏幕中，第一个步骤是创建套餐标识。  套餐标识由三个部分组成：“套餐 ID”、“发布者 ID”和“名称”。  以下部分将介绍其中的每个组成部分。

#### <a name="offer-id"></a>套餐 ID

此标识符是首次提交套餐时创建的唯一名称。 它只能包含小写的字母数字字符、短划线或下划线。 套餐 ID 将在 URL 中显示，并会影响搜索引擎的结果。 例如，*yourcompanyname\_exampleservice*

如示例中所示，套餐 ID 将追加到发布者 ID 的后面，以创建唯一的标识符。 此字符串将公开为可预订服务的永久链接，并由搜索引擎编制索引。 

套餐上线后，无法更新其标识符

#### <a name="publisher-id"></a>发布者 ID

此标识符与你的帐户相关。 使用组织帐户登录后，发布者 ID 将显示在下拉菜单中。

#### <a name="name"></a>名称

此字符串是显示在 AppSource 或 Azure 市场中的套餐名称。

**重要提示：** 此处只能输入实际服务的名称。 不要包含服务的持续时间和类型。

以下 Edgewater Fullscope 示例演示了如何组合套餐名称。 套餐名称显示为：

![创建新的咨询服务套餐](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

套餐名称由四个部分组成：

-   **持续时间：**- 在编辑器的“店面详细信息”选项卡中定义。 可使用小时、天或周作为单位来表示持续时间。
-   **服务类型：**- 在编辑器的“店面详细信息”选项卡中定义。 服务类型包括 `Assessment`、`Briefing`、`Implementation`、`Proof of concept` 和 `Workshop`。
-   **介词：**- 由审阅者插入
-   **名称：**- 在“套餐设置”页中定义。

以下列表提供了几个适当的套餐名称：

-   专业服务基础知识：1 小时简报
-   云迁移平台：1 小时简报
-   PowerApps 和 Microsoft Flow：1 日研讨会
-   Azure 机器学习服务：3 周 PoC
-   实体与虚拟零售解决方案：1 小时简报
-   自带数据：1 周研讨会
-   云分析：3 日研讨会
-   Power BI 培训：3 日研讨会
-   销售管理解决方案：1 周实现
-   CRM 快速入门：1 日研讨会
-   Dynamics 365 销售版：2 日评估

完成“套餐设置”选项卡中的操作后，可以保存提交内容。 现在，套餐名称会显示在编辑器的上方，并且可以在“所有套餐”中找到它。

### <a name="enter-storefront-details"></a>输入店面详细信息

接下来，需要输入店面的详细信息。 “店面详细信息”由以下部分组成：

-   套餐详细信息
-   发布者信息
-   商品详细信息
-   营销项目

#### <a name="offer-details"></a>套餐详细信息

套餐详细信息部分包含以下字段：

-   套餐摘要
-   套餐说明

##### <a name="offer-summary"></a>套餐摘要

套餐摘要是套餐的简要说明，紧靠在套餐名称的下面显示。 输入套餐摘要时请使用纯文本，且不要换行。 下面是适当的套餐摘要及其相应套餐名称的示例：

*示例 1*

-   **套餐名称：** 云分析：3 日研讨会
-   **套餐摘要：** Microsoft Azure 和 Power BI、当前环境评估与微型 POC 的概述。

*示例 2*

-   **套餐名称：** 行业 Azure IoT：30 日概念证明
-   **套餐摘要：** 创建行业联网试点产品，以便将现场设备安全连接到提供仪表板、报告和通知的 Azure IoT 中心解决方案。

*示例 3*

-   **套餐名称：** 专业服务：1 小时简报
-   **套餐摘要：** 预配置扩展型 Dynamics 365 运营版解决方案的概述和演示，此版本可为专业服务提供增强的项目、计费和资源管理。

*示例 4*

-   **套餐名称：** Your World 中的 power BI：4 小时研讨会
-   **套餐摘要：** 开始运行第一个仪表板并了解最佳做法。 现场举行，参与者不超过 12 名学生。

*示例 5*

-   **套餐名称：** Dynamics 和项目：3 日评估
-   **套餐摘要：** 针对 ERP 解决方案的需求收集与评估，该解决方案专为专业服务公司和项目驱动型而企业设计。

##### <a name="offer-description"></a>套餐说明

咨询服务套餐的说明。 合理的套餐说明包括互动功能的具体详细信息，以及最终向客户交付的内容。 此说明应该可以帮助客户明确了解他们会得到的服务。

不要在套餐说明中包含电子邮件链接或联系电话号码。 套餐中应该附带一个“与我联系”按钮，使用该按钮可将潜在顾客上传到你在套餐中指定的潜在顾客管理目标。

以 Markdown 格式输入套餐说明。 如果你不熟悉 Markdown 或 HTML 格式，可以查看 [Microsoft Docs 站点](https://docs.microsoft.com/contribute/how-to-write-use-markdown)上的资源。

使用这些格式可确保客户能够最方便地阅读你的套餐内容。

套餐说明应该简单扼要，并遵守字符数限制，因为用户并不乐意阅读很长的文字。 但仍可以选择上传更详细地描述套餐的营销手册、事实表和其他文档。

以下示例演示了一段适当编写的套餐说明及其相关名称和摘要：

**套餐名称：** 云分析：3 日研讨会

**套餐摘要：** Microsoft Azure 和 Power BI、当前环境评估与微型 POC 的概述。

**套餐说明：** 这场为期 3 天的研讨会面向技术和业务主管，在客户现场举行。

***议程***

第 1 天

-   重点探讨如何使用 Azure Data Lake、HDInsight 或 Azure SQL 数据仓库在 Microsoft 云中保护、缩放和组织数据。

第 2 天

-   探讨如何使用 Microsoft R 和 Azure 机器学习来配置和部署高级分析解决方案。

第 3 天

-   探讨如何使用 Power BI 来提取可行见解并将分析结果操作化，包括举行一场协作短会来共同生成 Power BI 仪表板。

此次研讨会结束时，客户可为 Microsoft 云中的数据和分析解决方案定义高级计划和实施路线图。

*遵循此格式的套餐示例 Markdown 文件：*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**发布者信息**

**MPN ID**

9 位数 Microsoft 合作伙伴网络 (MPN) ID。 如果没有 MPN ID，可以转到 Microsoft 合作伙伴中心获取一个 ID。

**合作伙伴中心 ID**

新的合作伙伴中心 ID（如果有）。

**MPN ID**

输入机密密钥，以便在套餐上线之前，在 AppSource 中对其进行预览。
此标识符并非密码。

#### <a name="listing-details"></a>商品详细信息

**咨询服务类型**

Microsoft 专门注重提供给单个客户的固定范围、固定持续时间、估算价格、固定价格（或免费），以及主要面向售前的咨询服务套餐，另外还关注在现场或以虚拟形式开展的评估、实施、概念证明、简报或研讨会套餐（简报必须在现场开展）。

包括以下五种类型的套餐：

-   **评估：** 评估客户的环境，以确定解决方案的适用性，并提供成本和时间估算。
-   **简报：** 使用框架、演示和客户示例介绍解决方案或咨询服务，以吸引客户的兴趣。 简报必须在现场开展。
-   **实施：** 进行完整安装，以实施完全正常运行的解决方案。 对于此试点套餐，Microsoft 建议限制为可在一周或更短时间内实施的解决方案。
-   **概念证明：** 进行有限范围的实施，以确定解决方案是否满足客户的要求。
-   **研讨会：** 在客户现场开展的互动活动，可包括培训会议、简报、评估，或根据客户数据或环境制作的演示。

**国家/区域可用性**

选择提供此咨询服务套餐的国家和地区。 单个套餐不能在多个国家/地区发布。 必须为每个国家或地区创建一个新套餐。

**行业**

选择咨询服务套餐最适合的行业。

**持续时间**

在“持续时间”下选择一个数字（例如 3、4，等等），然后选择“小时”、“天”或“周”。

**主要产品**

若要发布到 Azure 市场，请选择“Azure”作为主要产品，然后选择相关的“解决方案区域”。

若要发布到 AppSource，请选择“Dynamics 365”、“Power BI”或“PowerApps”作为主要产品。 还可以选择其他相关的“适用产品”，你的咨询服务套餐将显示在 AppSource 上与其中每个产品关联的商品清单中。

**相关资质**

选择与此套餐相关的资质，以便与套餐详细信息一同显示。

#### <a name="marketing-artifacts"></a>营销项目

**公司徽标（.png 格式，48 x 48 像素）**

上传要显示在套餐库视图页中套餐磁贴上的图像。 该图像必须是 .png 格式，分辨率为 48 x 48 像素。

**公司徽标（.png 格式，216 x 216 像素）**

上传要显示在套餐详细信息页上的图像。 该图像必须是 .png 格式，分辨率为 216 x 216 像素。

**视频（最多 4 个）**

最多上传四个客户案例研究视频或客户参考视频。 如果没有此类视频，请上传一个视频来介绍贵公司在套餐方面的专业技术。 如果有 Power BI 或 PowerApps 解决方案的展示，请在此处上传展示视频。 视频必须链接到 YouTube 或 Vimeo。

**文档（最多 3 个）**

上传详细描述咨询服务套餐的营销手册。 或者，也可以上传公司概况、事实表或案例研究。

**屏幕截图（最多 5 个）**

最多上传五个图像，以提供有关套餐、套餐交付件或公司的详细信息。 例如，营销手册的片段、演示文稿中的相关幻灯片，或者展示公司发展势头或专业知识的图像。

### <a name="publish-your-offer"></a>发布套餐

填写“套餐设置”、“店面详细信息”和“联系人”后，选择“发布”并提供电子邮件地址。 当 Microsoft 准备好发布你的套餐时，会向你发送一封电子邮件，可在其中预览该套餐上线之前的外观。 随时可以返回门户检查套餐的状态。

在发布过程中，套餐可能显示为“发布已取消”或“发布失败”状态。 此状态在发布过程中是正常的，Microsoft 可借此机会对套餐进行编辑。 如果看到套餐处于“发布已取消”状态，请让它保持该状态。
