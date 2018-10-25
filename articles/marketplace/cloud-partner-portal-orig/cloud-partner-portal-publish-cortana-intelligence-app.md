---
title: Cortana Intelligence | Microsoft Docs
description: 发布 Cortana Intelligence 产品/服务。
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: f12a15a0d739ae6e98be3871fa8bb4104f49565c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805851"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>使用云合作伙伴门户发布 Cortana Intelligence 产品/服务

本文介绍了如何使用云合作伙伴门户发布 Cortana Intelligence 产品/服务。

## <a name="prerequisites"></a>先决条件

云合作伙伴门户支持对门户进行基于角色的访问，这样参与者就能协作发布产品/服务了。 有关详细信息，请参阅[在云门户上管理用户](./cloud-partner-portal-manage-users.md)。

具有“所有者”角色的个人之一必须先同意遵守[使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隐私声明](http://www.microsoft.com/privacystatement/default.aspx)和 [Microsoft Azure 认证计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)，然后才能以发布者帐户的身份发布产品/服务。

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>开始创建 Cortana Inteligence 产品/服务的具体步骤

满足所有先决条件后，便能开始创建 Cortana Inteligence 产品/服务。

1.  登录[云合作伙伴门户](http://cloudpartner.azure.com/)。
2.  在左侧导航栏中，选择“+ 新产品/服务”。
3. 选择“Cortana Intelligence”。
4. 选择“新建产品/服务”视图中的“编辑器”选项卡，以配置下列选项：
    -   产品/服务设置
    -   技术信息
    -   店面详细信息
    -   联系人

    上面所有选项都显示可填写的表单。每个表单的必填字段标有红色星号 (\*)。

## <a name="to-configure-offer-settings"></a>配置“产品/服务设置”的具体步骤

“产品/服务设置”提供了产品/服务的基本信息，如产品/服务 ID、发布者 ID 和产品/服务名称。

### <a name="offer-id"></a>套餐 ID

这是发布者个人资料内产品/服务的唯一标识符。
此 ID 显示在产品 URL 中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不得以破折号结尾，且长度不得超过 50 个字符。 
>[!Note]
>此字段在产品/服务正式上线后处于锁定状态。

**示例：**

如果发布者 contoso 创建的产品/服务的产品/服务 ID 为“sample-Cortana Intelligence”，AppSource 中便会显示“https://appsource.microsoft.com/marketplace/apps/contoso.sample-Cortana Intelligence?tab=Overview”。

### <a name="publisher-id"></a>发布者 ID

发布者配置文件的下拉列表。 用于选择要在哪个发布者配置文件下发布此产品/服务。

>[!Note]
>此字段在产品/服务正式上线后处于锁定状态。

### <a name="name"></a>名称

产品/服务的显示名称。 此名称显示在 [AppSource](https://appsource.microsoft.com) 中。 该名称不能超过 50 个字符。

提供完“产品/服务设置”中需要的信息后，选择“保存”，以转到产品/服务的“技术信息”选项部分 。

## <a name="to-configure-technical-info"></a>配置“技术信息”的具体步骤

此视图可用于提供产品/服务页中显示的技术信息。 此视图包含以下字段。

### <a name="partner-mpn-id"></a>合作伙伴 MPN ID

若为已注册 Microsoft 合作伙伴，请转到并登录[合作伙伴网站](https://partners.microsoft.com/)，以获取组织合作伙伴 ID。在“合作伙伴 MPN ID”中输入此 ID。

### <a name="analytics-components-used"></a>使用的分析组件

选择产品/服务使用的所有组件。 至少选择 1 个组件。 仅当使用 SQL 2016 R 服务、高级 HDInsight 或 VM 上运行的 MRS 的任一 MRS 许可证时，才选择“Microsoft R”。

### <a name="additional-components-used"></a>使用的其他组件

选择解决方案使用的其他所有组件。

### <a name="customer-name-using-solution"></a>使用解决方案的客户名称

提供在生产中使用此解决方案的客户名称。 

>[!Note]
>此信息不会发布在 AppSource 上， 仅用于解决方案评估目的。

### <a name="azure-consumption-requirement-met"></a>是否满足 Azure 使用要求?

指明解决方案是否每月每 Azure 套件组件客户至少生成 1000 美元，或解决方案是否使用 Microsoft R。

>[!Note]
>此信息不会发布在 AppSource 上， 仅用于解决方案评估目的。

### <a name="demo-video-url"></a>演示视频 URL

提供解决方案/应用的演示视频 URL，以供售前团队向客户展示。 

>[!Note]
>此信息不会发布在 AppSource 上， 仅用于解决方案评估目的。

#### <a name="demo-video-guidelines"></a>演示视频指南

- 视频长度应短于 15 分钟。
- 视频至少应编辑录制解决方案的功能。 
- 视频应突出面向用户的功能的关键方面，并以高级分析集成为重点。 
- 演示视频**不会**公开给客户客，但有望作为如何*向*潜在客户展示解决方案的代表。 因此，演示视频应脚本化并可重复观看。
- 使用任何可导出标准视频格式（例如，MPEG）的屏幕录制工具来创建视频。 

下面介绍了如何使用 Skype for Business 创建视频。 

1. [开始会议](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [分享桌面](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [开始录制](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. 停止录制后，请[使用录制管理器发布录制](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

将录制的视频上传到允许生成共享 URL 的服务。 例如，[OneDrive 或 Sharepoint 中的来宾链接](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)。

### <a name="supported-regions"></a>支持的区域

选择解决方案支持的所有区域。 至少选择 1 个区域。

### <a name="power-bi-desktop-file-pbix"></a>Power BI Desktop 文件(.pbix)

上传 .pbix 文件（若有）。 请务必将数据导入此文件，而不是在外部引用数据。 我们将会为你创建嵌入报表。

### <a name="solution-architecture"></a>解决方案体系结构

上传详述解决方案体系结构的文档。 若要了解如何上传解决方案体系结构图表，请参阅[高级分析解决方案工作流模板](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)。

>[!Note]
>此信息不会发布在 AppSource 上， 仅用于解决方案评估目的。

### <a name="select-segments"></a>选择细分

选择所有相关的行业细分。 如果应用的细分未列出，请在“其他”字段中输入细分名称。 输入的细分最多只能包含三个字词。

### <a name="select-business-processes"></a>选择业务流程

选择最准确描述解决方案的业务流程。 如果应用的流程未列出，请在“其他”字段中输入流程名称。 输入的流程最多只能包含三个字词。

### <a name="trial-info"></a>试用信息

-   **SaaS 试用版 URL：** 输入应用试用版体验的 URL。
-   **体验版试用 URL：** 输入应用体验版体验的 URL。

若要详细了解试用信息，请参阅本文下一部分中的“应用类型”。

提供完“技术信息”中需要的信息后，选择“保存”，以转到产品/服务的“店面详细信息”部分。 

## <a name="to-configure-storefront-details"></a>配置“店面详细信息”的具体步骤

### <a name="offer-summary"></a>产品/服务摘要

这是产品/服务的价值主张摘要。 它将显示在产品/服务的搜索页上。 此摘要的长度上限为 100 个字符。

### <a name="offer-description"></a>产品/服务描述

这是显示在应用的详细信息页上的说明。
此说明的长度上限为 1300 个字符。

请注意，此字段需要使用包含 \<p\>、\<h1\>、\<h2\>、\<li\> 等标记的 html 内容，以提高内容的可展示性。 发布门户团队正在努力推出一项功能，以便用户能够预览店面详细信息，以迭代方式提高内容的可展示性，同时还能使用任何联机实时 html 工具（如 [http://htmledit.squarefree.com](http://htmledit.squarefree.com/)）来查看说明的呈现效果。

建议采用以下说明格式：根据价值主张将文本分成多个子部分，每个子部分都有突出显示的子标题。 访问者通常会浏览“产品/服务摘要”字段和子标题，快速简要地了解应用所解决的问题以及为什么应考虑使用此应用。 因此，请务必要吸引用户注意力，让他们有理由继续阅读说明来获取具体信息。

#### <a name="partner-examples"></a>合作伙伴示例

- [Neal Analytics 库存优化](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure 零售个性化](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint 公民服务](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>行业

选择应用最适合的行业。 如果应用与多个行业相关，请将此字段留空。

### <a name="categories"></a>类别

选择与应用相关的类别。 最多选择两个类别。

### <a name="app-type"></a>应用类型

选择应用在 AppSource 中支持的试用类型。 从下列试用类型中进行选择：
- **免费**：表示应用是免费的。
- **试用**：表示客户可试用一段指定时间。
- **申请试用**：表示客户可申请试用应用。

合作伙伴可以在 AppSource 上提供两种类型试用体验。

- “试用”选项亦称为“客户引导试用 (CLT)”，可以是下列类型之一： 
    - SaaS 试用版
        - 客户可转到你或合作伙伴提供的 URL。 客户通过 AAD 联合 SSO 获取有时间限制的试用体验。
        - 这是多租户 SaaS 应用，将各个用户的配置/数据隔离开来。 或者，此体验仅提供支持只读操作的解决方案子集。

        **示例：**

        - [AFS POP 零售执行](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint 公民服务](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)。 （此应用为一组选定用户角色提供路径明确的精选体验。）

     - 体验版
        - 你（或合作伙伴）的解决方案或其子集可使用 AppSource 能实例化的 Azure 资源管理器模板进行打包。 AppSource 可以在有时间限制和维护热/冷实例池等的合作伙伴订阅中管理应用。
        - 如果你选择此选项，我们可以提供模板和示例代码来帮助你。

        **示例：**

        - [Neal Analytics 库存优化](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- 若要使用“申请试用”（亦称为“合作伙伴引导试用 (PLT)”）选项，客户必须填写联系信息表单，以供合作伙伴跟进。 合作伙伴会跟进，并提供应用演示或试用版。 有关详细信息，请观看 [AppSource 试用体验演练](http://aka.ms/trialexperienceforwebapps)视频，以了解简要概况。

>[!Note]
>数据表明，“客户引导试用”的潜在客户生成率高于“合作伙伴引导试用”。


### <a name="help-link-for-your-app"></a>应用的帮助链接

提供包含应用帮助信息的页面的 URL。

### <a name="supported-countriesregions"></a>支持的国家/地区

此字段确定产品/服务可供试用的国家/地区。

![支持的国家/地区](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>支持的语言

选择应用支持的语言。 如果应用支持的语言未列出，请发布产品/服务，并向 <appsource@microsoft.com> 发送电子邮件，告诉我们应用对其他语言的支持。

### <a name="app-version"></a>应用版本

输入应用的版本号。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

列出可与应用配合使用的具体产品。 最多可列出 3 个产品。 若要列出产品，请选择加号图标（位于“新建”旁边）。此时，系统会为你创建开放文本字段。 输入可与应用配合使用的产品名称。

### <a name="hide-key"></a>隐藏密钥

这是与产品/服务预览 URL 结合使用的密钥，用于对公众隐藏产品/服务。 这不是密码。 可输入任意字母数字字符串。

### <a name="offer-logo-small"></a>产品/服务徽标(小)

此徽标显示在应用的搜索页上。 仅允许使用 png 图像格式。 图像大小为 48 x 48 像素。

### <a name="offer-logo-large"></a>产品/服务徽标(大)

此徽标显示在应用的详细信息页上。 仅允许使用 png 图像格式。 图像大小为 48 x 48 像素。

### <a name="video"></a>视频

最多可上传 4 个视频。 对于要上传的每个视频：
- 提供视频名称
- 提供 URL（仅限 YouTube 或 Vimeo）
- 提供要与视频关联的缩略图。 缩略图必须使用 png 图像格式，且大小必须为 1280 x 720 像素。 

若要添加一个或多个新视频，请选择下一屏幕截图中的“+ 新建”。

![添加新视频](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>文档

最多可上传 3 个文档。 每个文档都必须使用 PDF 文件格式。 若要添加新文档，请执行以下操作：

- 选择“+ 新建”
- 输入文档名称
- 选择“上传”，以上传文档。

![添加新文档](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>隐私策略

输入应用的隐私策略 URL

### <a name="terms-of-use"></a>使用条款

输入应用的使用条款。 AppSource 客户必须接受这些条款，才能试用应用。

>[!Note]
>此字段接受使用 HTML 标记的内容，如 html 内容 <p\>、<h1\> 和 <li\>。 这些标记可用于设置内容格式。 

### <a name="lead-destination"></a>潜在顾客目标

选择用于存储潜在客户的 CRM 系统。 

如果使用的是下列 CRM 系统之一，请选择“Azure 表”：Salesforce、Marketo 或 Microsoft Dynamics CRM。 

若要详细了解要使用的 CRM 系统，请选择以下受支持系统的链接之一。

-   [Azure 表](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
