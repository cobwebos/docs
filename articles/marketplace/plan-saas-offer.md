---
title: 如何为 Microsoft 商用 marketplace 规划 SaaS 产品/服务
description: 如何使用 Microsoft 合作伙伴中心的 "商用 Marketplace" 计划规划新的软件即服务 (SaaS) 产品/服务，以便在 Microsoft AppSource、Azure Marketplace 或通过云解决方案提供商 (CSP) 程序中列出或销售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 8dfc1eb35572a6b706deb47335357417bd837825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819931"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>如何为商业市场规划 SaaS 产品/服务

本文介绍了将软件作为服务发布到 Microsoft 商用 marketplace (SaaS) 产品/服务的不同选项和要求。 SaaS 产品/服务可让你通过联机订阅（而不是在单独的计算机上进行本地安装）向你的客户提供软件解决方案并提供许可证。 本文将帮助你准备产品/服务，以便通过合作伙伴中心发布到商业市场。

## <a name="listing-options"></a>列表选项

准备发布新的 SaaS 产品/服务时，需要确定要选择的 _列表_ 选项。 这会确定在合作伙伴中心创建产品/服务时需要提供的其他信息。 你将在 "  **产品/服务设置** " 页上定义你的列表选项，如 [如何在商业应用商店中创建 SaaS 产品](create-new-saas-offer.md)中所述。

下表显示了商业应用商店中 SaaS 产品/服务的列表选项。

| 列表选项 | 事务处理 |
| ------------ | ------------- |
| 与我联系 | 客户直接从列表中的信息与你联系。``*`` |
| 免费试用 | 客户通过 Azure Active Directory (Azure AD) 重定向到目标 URL。``*`` |
| 立即获取 (免费)  | 客户通过 Azure AD 重定向到目标 URL。``*`` |
| 通过 Microsoft 进行销售  | 通过 Microsoft 销售的产品/服务称为 _事务_ 产品。 这是一个事务的产品/服务，Microsoft 可在其中代表出版商提供软件许可证的资金。 我们使用你选择的定价模型来计费 SaaS，并代表你管理客户交易。 请注意，Azure 基础结构使用费用会直接向你计费。 你应考虑定价模型中的基础结构成本。 下面的 [SaaS 计费](#saas-billing) 中更详细地说明了这一点。  |
|||

``*`` 发布者负责支持软件许可证事务的所有方面，包括但不限于订单、履行、计量、计费、开票、支付和收集。

有关这些列表选项的详细信息，请参阅 [商业市场事务处理功能](marketplace-commercial-transaction-capabilities-and-considerations.md)。

发布产品/服务后，你为产品/服务选择的列表选项将显示为产品/服务列表页面左上角的按钮。 例如，以下屏幕截图显示了 Azure Marketplace 中的 "产品/服务列表" 页，其中包含 " **联系我** " 和 " **测试驱动器** " 按钮。

![说明在线商店中的产品/服务列表。](./media/listing-options.png)

## <a name="technical-requirements"></a>技术要求

技术要求不同，具体取决于你为产品/服务选择的列表选项。

" _与我联系_ " 列表选项没有技术要求。 你可以选择连接 CRM 系统来管理客户主管，这将在本文后面的 [客户领导](#customer-leads) 部分中介绍。

_现在 (免费__试用版) 、免费试用版_和_通过 Microsoft_列表选项销售的内容具有以下技术要求：

- SaaS 应用程序必须是多租户解决方案。
- 你可以 (MSA) 启用 Microsoft 帐户，并 [Azure Active Directory (Azure AD) ](https://azure.microsoft.com/services/active-directory/) 来验证用户。
- 必须创建登录页。 用户购买你的产品/服务后，会将其定向到登陆页面，以方便所需的任何其他设置或设置。 有关创建登陆页面的指南，请参阅以下文章：
  - [在商业应用商店中构建事务 SaaS 产品/服务的登陆页面](azure-ad-transactable-saas-landing-page.md)
  - [在商业应用商店中为免费或试用版 SaaS 服务构建登陆页面](azure-ad-free-or-trial-landing-page.md)

这些额外的技术要求仅适用于 _通过 Microsoft_ (事务) 列表选项销售：

- Azure AD 具有单一登录 (SSO) 标识管理和身份验证是购买用户访问登陆页面所必需的。 有关详细指南，请参阅 [商业应用商店中的 Azure AD 和事务 SaaS 产品](azure-ad-saas.md)。
- 必须使用 [SaaS 履单 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 与 Azure Marketplace 和 Microsoft AppSource 集成。 你需要公开可以与 SaaS 订阅交互的服务，以创建、更新和删除用户帐户和服务计划。 必须在 24 小时内支持关键 API 更改。 非关键 API 更改将定期发布。 适用于 [api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)的文档中提供了介绍所收集字段的用法的关系图和详细说明。
- 您必须为您的产品/服务创建至少一个计划。 你的计划将基于你在发布之前选择的定价模型： _平面速率_ 或 _每用户_。 本文后面提供了有关 [计划](#plans) 的更多详细信息。
- 客户可随时取消你的产品/服务。

### <a name="technical-information"></a>技术信息

如果要创建事务产品/服务，则需要收集 " **技术配置** " 页的以下信息。 如果选择单独处理事务而不是创建事务产品/服务，请跳过此部分并转到 " [测试驱动器](#test-drives)"。

- **登陆页 url**： SAAS 网站 url (例如： `https://contoso.com/signup` 从商业市场获取产品/服务后，用户将定向到的) ，从新创建的 SaaS 订阅触发配置过程。 此 URL 将收到一个令牌，该令牌可用于调用履单 Api 以获取交互式注册页面的预配详细信息。

  将通过唯一标识特定客户的 SaaS 购买的 marketplace 购买标识令牌参数调用此 URL。 必须使用 [解析 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)为相应的 SaaS 订阅详细信息交换此标记。 应将这些详细信息以及要收集的任何其他内容用作客户交互式网页的一部分，以完成客户注册并激活其购买。 在此页上，用户应通过使用 Azure Active Directory (Azure AD) 注册一次单击身份验证。

  当客户从 Azure 门户或 M365 管理中心启动托管 SaaS 体验时，还将调用具有 marketplace 购买标识令牌参数的 URL。 应处理这两个流：在新客户购买后首次提供令牌时，以及再次为管理其 SaaS 解决方案的现有客户提供。

    你配置的登陆页面应已启动并运行24/7。 这是您在商业应用商店中购买的 SaaS 产品或产品/服务的活动订阅的新购买通知的唯一方法。

- **连接 webhook**：对于 Microsoft 需要发送给你的所有异步事件 (例如，当 SaaS 订阅已取消) 时，我们需要你提供连接 webhook URL。 我们将调用此 URL 来通知你事件。

  你提供的 webhook 应启动并运行24/7，因为这是你将会收到有关通过商业 marketplace 购买的客户 SaaS 订阅更新的通知。

  > [!NOTE]
  > 在 Azure 门户中，我们需要创建单租户 [Azure Active Directory (Azure AD) 应用程序](../active-directory/develop/howto-create-service-principal-portal.md) ，以便使用一个 Azure 应用 ID 对两个服务之间的连接进行身份验证。 若要查找 [租户 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，请跳到 Azure Active Directory，选择 " **属性**"，然后查找列出的目录 ID 号。 例如，`50c464d3-4930-494c-963c-1e951d15360e`。

- **Azure Active Directory 租户 id**： (也称为目录 ID) 。 在 Azure 门户中，我们要求你 [注册 Azure Active Directory (AD) 应用程序](../active-directory/develop/howto-create-service-principal-portal.md) ，以便可以将其添加到 API (ACL) 的访问控制列表，以确保你有权调用该应用程序。 若要查找 Azure Active Directory (AD) 应用的租户 ID，请参阅 Azure Active Directory 中的 [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 边栏选项卡。 在 " **显示名称** " 列中，选择应用。 然后查找 (列出的 **目录 (租户) ID** 号，例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

- **Azure Active Directory 应用程序 id**：还需要 [应用程序 id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 若要获取其值，请转到 Azure Active Directory 中的 " [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) " 边栏选项卡。 在 " **显示名称** " 列中，选择应用。 然后查找 (列出的应用程序 (客户端) ID 号，例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

  Azure AD 应用程序 ID 与你的合作伙伴中心帐户中的发布者 ID 相关联。 您必须对该帐户中的所有产品/服务使用相同的应用程序 ID。

  > [!NOTE]
  > 如果发布者在合作伙伴中心有两个或更多不同的帐户，则应使用两个或多个不同的 Azure AD 应用 Id，每个帐户对应于一个帐户。 伙伴中心中的每个合作伙伴帐户应为通过此帐户发布的所有 SaaS 产品/服务使用唯一的 Azure AD 应用 ID。

## <a name="test-drives"></a>体验版
可以选择为 SaaS 应用启用测试驱动器。 测试驱动器允许客户在固定的小时数内访问预配置环境。 可以为任何发布选项启用测试驱动器，但此功能具有其他要求。 若要了解有关测试驱动器的详细信息，请参阅 [什么是测试驱动器？](what-is-test-drive.md)。 有关配置不同种类的测试驱动器的信息，请参阅 [测试驱动器技术配置](test-drive-technical-configuration.md)。

> [!TIP]
> 测试驱动器与 [免费试用版](plans-pricing.md#free-trials)不同。 你可以提供一个测试驱动器和/或免费试用版。 它们都为你的客户提供了一个固定时间段的解决方案。 但在实际实施方案中，一个测试驱动器还包括一项针对您的产品关键功能的动手自学教程。

## <a name="customer-leads"></a>潜在顾客

您必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及提供程序的名称、ID 和在线商店，将被发送到已配置的 CRM 系统。 商业应用商店支持各种 CRM 系统，以及使用 Azure 表或使用电源自动配置 HTTPS 终结点的选项。

你可以在创建产品/服务或之后随时添加或修改 CRM 连接。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="selecting-an-online-store"></a>选择在线商店

发布 SaaS 产品/服务时，它将列在 Microsoft AppSource 和/或 Azure Marketplace 中。 每个在线商店都有独特的客户需求。 AppSource 适用于业务解决方案，Azure Marketplace 适用于 IT 解决方案。 你的产品/服务类型、功能和类别将确定你的产品/服务的发布位置。 类别和子类别基于解决方案类型映射到每个在线商店。 有关选择在线商店的详细信息，请参阅 [选择在线商店](determine-your-listing-type.md#selecting-an-online-store)。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 提供了一个标准合同，你可以将其用于商业应用商店中的产品/服务。 当你在标准合同下提供软件时，客户只需阅读并接受一次，你就不必创建自定义条款和条件。

如果选择使用标准协定，则可以选择将通用修正术语和最多10个自定义改正添加到标准协定。 你还可以使用自己的条款和条件，而不是标准协定。 你将在 " **属性** " 页中管理这些详细信息。 有关详细信息，请参阅 [Microsoft 商用 marketplace 的标准约定](standard-contract.md)。

> [!NOTE]
> 使用商业应用商店的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 它是“或”方案。 你可以在标准合同或你自己的条款和条件下提供解决方案。 如果要修改标准约定的条款，可以通过标准协定修正来实现。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

在合作伙伴中心 [创建新的 SaaS 产品](create-new-saas-offer.md) /服务时，你将在 " **产品/服务列表** " 页上输入文本、图像、可选视频和其他详细信息。 这是客户在商业市场中发现你的产品/服务列表时将看到的信息，如以下示例中所示。

:::image type="content" source="./media/example-saas-1.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。&quot;:::

**拨出说明**

1. 徽标
2. 类别
3. 行业
4. 支持地址 (链接) 
5. 使用条款
6. 隐私策略
7. 产品名称
8. 总结
9. 说明
10. 屏幕截图/视频
11. 文档

下面的示例演示了 Azure 门户中的产品/服务列表。

![说明了 Azure 门户中的产品/服务列表。](./media/example-managed-services.png)

**调出说明**

1. 标题
1. 说明
1. 有用的链接
1. 屏幕截图

> [!NOTE]
> 如果产品/服务说明以短语 &quot;此应用程序只能在 [非英语] 中使用&quot;，则产品/服务列表内容不需要为英语。

为了帮助更轻松地创建你的产品/服务，请提前准备其中一些项。 除非另有说明，否则需要以下各项。

- **名称**：此名称将显示为商业应用商店中的产品/服务列表的标题。 该名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且长度必须限制为 50 个字符。
- **搜索结果摘要**：将产品/服务的用途或功能描述为一个句子，其中不含分行符的100个字符或更少。 此摘要用于商业应用商店，其中列出了) 搜索结果 (。
- **说明**：此说明将显示在 &quot;商用 marketplace&quot; 列表中 (&quot;) 概述&quot;。 考虑包括值主张、关键优势、预期用户群、任何类别或行业关联、应用内购买机会、任何所需的披露以及用于了解详细信息的链接。
    
    此文本框包含丰富的文本编辑器控件，你可以使用这些控件来使说明更具吸引力。 你还可以使用 HTML 标记来设置说明格式。 在此框中最多可以输入3000个字符，包括 HTML 标记。 有关其他提示，请参阅[编写出色的应用说明](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)。

- **入门说明**：如果你选择通过 Microsoft (事务产品/服务销售产品/服务) ，则此字段是必需的。 这些是帮助客户连接到 SaaS 产品/服务的说明。 最多可以添加3000个字符的文本，并链接到更详细的联机文档。
- **搜索关键字** (可选) ：提供多达三个搜索关键字，客户可以使用这些关键字在在线商店中查找你的产品/服务。 不需要包含产品/服务 **名称** 和 **说明**：文本自动包含在搜索中。
- **隐私策略链接**：你的公司隐私策略的 URL。 你必须提供有效的隐私策略，并负责确保你的应用符合隐私法律和法规。
- **联系信息**：必须指定组织中的以下联系人：
  - **支持联系人**：提供客户打开票证时要使用的 Microsoft 合作伙伴的姓名、电话和电子邮件。 还必须包含支持网站的 URL。
  - **工程联系**：提供姓名、电话和电子邮件，以便 Microsoft 在产品/服务出现问题时直接使用。 此联系信息未在商业应用商店中列出。
  - **Csp 计划联系人** (可选) ：如果你选择了 CSP 计划，请提供姓名、电话号码和电子邮件，以便这些合作伙伴可以与你联系，以解决任何问题。 你还可以将 URL 包含到营销材料。
- **有用的链接** (可选) ：你可以为产品/服务的用户提供各种资源的链接。 例如，论坛、Faq 和发行说明。
- **支持文档**：最多可以提供三个面向客户的文档，如白皮书、小册子、清单或 PowerPoint 演示文稿。
- **媒体–徽标**：提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小型** 和 **中型** 徽标。 稍后可以选择将它们替换为不同的图像。

   - 大型 (从 216 x 216 到 350 x 350 px，必需) 
   - 中型 (90 x 90 px，可选) 
   - 小写 (48 x 48 px，可选) 

  这些徽标用于在线商店中的不同位置：

  - 小徽标出现在 Azure Marketplace 搜索结果中，并出现在 &quot;Microsoft AppSource&quot; 主页和 &quot;搜索结果&quot; 页上。
  - 在 Microsoft Azure 中创建新资源时，将显示 &quot;中" 徽标。
  - 大徽标显示在 Azure Marketplace 中的产品/服务列表页上，并 Microsoft AppSource。

- **媒体-屏幕截图**：必须至少添加一个最多5个屏幕截图，其中包含以下要求，其中显示了产品/服务的工作原理：
  - 1280 x 720 像素
  - .png 文件
  - 必须包含标题
- **媒体-视频** (可选) ：最多可以添加四个具有以下要求的视频，其中演示了产品/服务：
  - 名称
  - URL：必须仅托管在 YouTube 或 Vimeo 上。
  - 缩略图： 1280 x 720 .png 文件

> [!Note]
> 你的产品/服务必须满足一般的 [商业市场认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general) 和 [软件即服务策略](https://docs.microsoft.com/legal/marketplace/certification-policies#1000-software-as-a-service-saas) ，才能发布到商业市场。

## <a name="preview-audience"></a>预览版受众
预览观众可以在现场发布之前访问你的产品/服务，以便在将其发布之前测试端到端功能。 在 " **预览观众** " 页上，可以定义受限的预览受众。 如果你选择单独处理事务，而不是通过 Microsoft 销售你的产品/服务，则此设置不可用。 如果是这样，则可以跳过本部分并转到 [其他销售机会](#additional-sales-opportunities)。

> [!NOTE]
> 预览受众不同于私有计划。 私有计划仅适用于所选的特定群体。 这使您可以与特定客户协商自定义计划。 有关详细信息，请参阅下一节：计划。

可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动添加最多10个电子邮件地址，或使用 .csv 文件最多导入20个电子邮件地址。 如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

## <a name="plans"></a>Plans

事务产品/服务需要至少一个计划。 计划定义解决方案范围和限制以及关联的定价。 你可以为你的产品/服务创建多个计划，为你的客户提供不同的技术和定价选项。 如果选择单独处理事务而不是创建事务产品/服务，则不会显示 " **计划** " 页。 如果是这样，请跳过本部分并转到 [其他销售机会](#additional-sales-opportunities)。

有关计划的一般指导，请参阅 [商业 marketplace 的计划和定价](plans-pricing.md) ，包括定价模型、免费试用版和私有计划。 以下部分介绍特定于 SaaS 产品/服务的其他信息。

### <a name="saas-pricing-models"></a>SaaS 定价模型

SaaS 产品/服务可对每个计划使用两个定价模型中的一种： _平面速率_ 或 _每用户_。 同一产品/服务中的所有计划都必须与同一定价模型相关联。 例如，一个产品/服务不能有一个单层计划和一个每个用户的另一个计划。

**单层** –启用对产品/服务的每月或每年单层费率的访问。 这有时称为基于站点的定价。 使用此定价模型，你可以选择定义使用 marketplace 计量服务 API 的计费计划，以对客户进行计费，使其不会被平整速率所涵盖。 有关计费计费的详细信息，请参阅 [使用商业 marketplace 计量服务的 SaaS 计费计费](./partner-center-portal/saas-metered-billing.md)。 如果 SaaS 服务的使用行为突发，还应使用此选项。

**每个用户** -启用对你的产品/服务的访问，其价格基于可访问产品/服务的用户数量。 使用此基于用户的模型，你可以设置计划支持的最小和最大用户数量。 您可以根据用户数量创建多个计划来配置不同的价格点。 这些字段是可选的。 如果未选择此值，则会将用户数解释为不会限制 (最小值为1，最大值为1，并且服务可以支持) 。 这些字段可以作为计划更新的一部分进行编辑。

> [!IMPORTANT]
> 发布产品/服务后，不能更改定价模型。 此外，同一产品/服务的所有计划必须共享同一定价模型。

### <a name="saas-billing"></a>SaaS 计费

对于在 (发布者) Azure 订阅中运行的 SaaS 应用，将直接对基础结构使用情况计费;客户不会看到实际的基础结构使用费用。 应将 Azure 基础结构使用费用捆绑到软件许可证定价中，以弥补部署的基础结构的成本以运行解决方案。

SaaS 应用程序产品/服务通过 Microsoft 支持每月或每年使用计费 [计费服务](./partner-center-portal/saas-metered-billing.md)按固定费用、每用户或消耗收费计费。 商业应用商店运营于代理商模型，使出版商设置价格、Microsoft 帐单客户和 Microsoft 将收入支付给出版商，同时为代理商收费。

这是一种用于说明机构模型的成本和付款的示例细分。 在此示例中，Microsoft 向客户收取 $100.00 美元的软件许可证费用，并向发布者支付 $80.00 美元。

| 你的许可证费用 | $100 美元/月 |
| ------------ | ------------- |
| Azure 使用费用（D1/1 核） | 直接向发布者而不是客户收费 |
| 由 Microsoft 向客户收费 | $100.00 美元/月（发布者必须将所有产生的或者转嫁的基础结构费用纳入许可证费用中） |
| **Microsoft 收费** | **$100 美元/月** |
| Microsoft 将许可证费用的 80% 支付给你<br>`*` 对于合格的 SaaS 应用，Microsoft 将支付90% 的许可证成本| 80.00 美元/月<br>``*`` 90.00 美元/月 |
|||

** `*` 降低 Marketplace 服务费用**–对于在商业市场上发布的特定 SaaS 产品/服务，microsoft 将从 20% (降低其 marketplace 服务费用，如 microsoft Publisher 协议) 到10% 中所述。 为了使你的产品/服务 () 合格，你的产品/服务 () 必须已由 Microsoft 指定为 Azure IP 共同销售 incentivized。 至少必须在每个日历月结束之前至少满足5个 (5) 的工作日内才能接收该月的降低 Marketplace 服务费用。 降低的 Marketplace 服务费用还适用于 Azure IP 共同销售 incentivized Vm、托管应用以及通过商业市场提供的任何其他合格事务 IaaS 产品/服务。

## <a name="additional-sales-opportunities"></a>其他销售商机

你可以选择加入 Microsoft 支持的营销和销售渠道。 在合作伙伴中心创建产品/服务时，你将在此过程结束时看到两个选项卡：

- **通过 Csp 转售**：使用此选项可允许 Microsoft 云解决方案提供商 (CSP) 合作伙伴将你的解决方案作为捆绑产品/服务的一部分转售。 有关详细信息，请参阅 [云解决方案提供商计划](cloud-solution-providers.md) 。

- **与 Microsoft 共同销售**：此选项可让 microsoft 销售团队在评估其客户需求时考虑你的 IP 共同销售资格解决方案。 请参阅 [合作伙伴中心的共同销售选项](./partner-center-portal/commercial-marketplace-co-sell.md) ，详细了解如何准备产品/服务进行评估。

## <a name="next-steps"></a>后续步骤

- [如何在商业应用商店中创建 SaaS 产品/服务](create-new-saas-offer.md)
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)
