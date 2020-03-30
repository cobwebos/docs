---
title: 云解决方案提供商 |Azure 应用商店
description: 发布商现在可以通过 Microsoft 云解决方案提供商 （CSP） 合作伙伴渠道销售其产品/服务。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: dsindona
ms.openlocfilehash: 9d7cbdd5ad551ba48f08f62237d5384aa7998fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281657"
---
# <a name="cloud-solution-providers"></a>云解决方案提供商

除了通过[Microsoft 网络店面](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)公开提供产品外，软件产品还可以覆盖云解决方案提供商 （CSP） 计划中合作伙伴服务的数百万合格的 Microsoft 客户。

发布商根据选择加入（新产品/服务）配置 CSP 计划中的可用性产品/服务，使合作伙伴能够销售您的产品并为客户创建捆绑解决方案。

发布商负责向最终客户提供中断修复支持，并为 CSP 计划和/或客户中的合作伙伴提供联系支持的机制。 最佳做法是向 CSP 计划中的合作伙伴提供用户文档、培训和服务运行状况/中断通知（如适用），以便 CSP 计划中的合作伙伴能够处理来自客户的第 1 层支持请求。  

以下优惠有资格被选择由 CSP 计划中的合作伙伴出售：

- 软件即服务 （SaaS） 交易优惠
- 虚拟机 (VM)
- 解决方案模板
- 托管应用程序

> [!NOTE]
> 默认情况下，CSP 程序中的合作伙伴会选择容器和自带许可证 （BYOL） VM SKU。

## <a name="how-to-configure-an-offering"></a>如何配置产品/服务

CSP 计划选择加入设置在合作伙伴中心或云合作伙伴门户提供创建体验中配置。 [详细了解不断变化的发布者体验](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>合作伙伴中心选择加入

在合作伙伴中心，您可以在 CSP 经销商访问模块下找到选择加入体验。

![CSP 经销商受众](media/marketplace-publishers-guide/csp-reseller-audience.png)

在 CSP 经销商访问模块中，您可以选择三个选项：

- 选项一：CSP 计划中的任何合作伙伴
- 选项二：我选择的 CSP 计划中的特定合作伙伴
- 备选方案三：CSP计划中没有合作伙伴

#### <a name="option-one-any-partner-in-the-csp-program"></a>选项一：CSP 计划中的任何合作伙伴

![CSP 计划中的任何合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 通过选择此选项，CSP 计划中的所有合作伙伴都有资格将您的产品/服务转售给客户。

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>选项二：我选择的 CSP 计划中的特定合作伙伴

![我选择的 CSP 计划的特定合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

通过选择此选项，您可以授权 CSP 计划中的哪些合作伙伴有资格转售您的产品/服务。

要授权合作伙伴，请单击 **"选择 CSP 合作伙伴**"，并显示一个菜单，允许您按合作伙伴名称或 CSP Azure 活动目录 （AAD） 租户 ID 进行搜索。

![选择 CSP 菜单](media/marketplace-publishers-guide/csp-pop-up-module.png)

您可以应用搜索筛选器，如**国家/地区**、**能力**或**技能**。

![合作伙伴搜索的国家/地区、能力和技能筛选器](media/marketplace-publishers-guide/csp-add-resellers.png)

选择合作伙伴列表后，选择 **"添加**"。

![CSP 计划中授权合作伙伴的示例列表](media/marketplace-publishers-guide/csp-add-resellers-details.png)

显示所选合作伙伴列表的表将显示在 CSP 经销商访问台页面上。

![CSP 经销商访问台页面上包含合作伙伴列表的表](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

选择 **"保存草稿"** 以注册更改。

如果此优惠未发布，则需要发布产品/服务以使其提供给所选合作伙伴。

>[!NOTE]
>如果您授权给定区域的 CSP 计划中合作伙伴，他们可以将产品/服务出售给属于该特定区域的任何客户。 有关 CSP 优惠如何分类区域的详细信息，请参阅[云解决方案提供商计划区域市场和货币](https://docs.microsoft.com/partner-center/regional-authorization-overview)。

如果要更新已发布的优惠的 CSP 列表，请添加其他合作伙伴并选择**同步 CSP 访问群体**。

如果您有一个已具有授权合作伙伴列表的要约，并且希望为另一个产品/服务使用相同的列表，请使用**导入/导出**。 导航到具有 CSP 列表的要约，然后选择 **"导出 CSP"。** 该函数开发一个 .csv 文件，该文件可以导入到另一个产品/服务中。

#### <a name="option-three-no-partners-in-the-csp-program"></a>备选方案三：CSP计划中没有合作伙伴

![CSP 计划中没有合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

通过选择此选项，您将从 CSP 计划中选择您的产品/服务。 您可以随时更改此选择。

### <a name="cloud-partner-portal-opt-in"></a>云合作伙伴门户选择加入

在云合作伙伴门户中，选择加入设置在"应用商店"或"网店"选项卡上。选择 CSP 计划中特定合作伙伴的能力仅在合作伙伴中心提供。

![CPP 中的 CSP 选择加入经验](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>取消 CSP 计划中的合作伙伴授权

如果您已授权 CSP 计划中的合作伙伴，并且该合作伙伴已将产品转售给其客户，则不允许您取消该合作伙伴的授权。

如果 CSP 计划中的合作伙伴没有向客户销售您的产品，并且您希望在产品/服务发布后删除 CSP，请使用以下说明：

1. 转到["支持请求"页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单会自动为您填写。

   > [!NOTE]
   > 不要更改预填充的下拉菜单选择。

2. 要**选择产品版本**，请选择 **"实时产品/服务管理**"。
3. 对于**选择最能描述问题的类别**，请选择引用您的报价的类别。
4. 对于**选择最能描述问题的问题**，请选择 **"更新现有产品/服务**"。
5. 选择 **"下一步**"以定向到 **"问题详细信息"页面**，以了解有关您的问题的更多详细信息。
6. 使用**取消授权 CSP**作为问题标题并填写所需的其余部分。




## <a name="navigate-between-options"></a>在选项之间导航

使用此部分在三个 CSP 经销商选项之间导航。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>从选项 1 导航：CSP 计划中的任何合作伙伴

如果您的报价当前是**选项 1：CSP 计划中的任何合作伙伴**，并且您希望导航到其他两个选项之一，请使用以下说明创建请求：

1. 转到["支持请求"页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单会自动为您填写。

   > [!NOTE]
   > 不要更改预填充的下拉菜单选择。

2. 要**选择产品版本**，请选择 **"实时产品/服务管理**"。
3. 对于**选择最能描述问题的类别**，请选择引用您的报价的类别。
4. 对于**选择最能描述问题的问题**，请选择 **"更新现有产品/服务**"。
5. 选择 **"下一步**"以定向到 **"问题详细信息"页面**，以了解有关您的问题的更多详细信息。
6. 使用**取消授权 CSP**作为问题标题并填写所需的其余部分。

> [!NOTE]
> 如果您尝试导航到选项 2，并且 CSP 计划中的合作伙伴已将产品/服务转售给其客户，则该合作伙伴默认已位于您的授权合作伙伴列表中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>从选项 2 导航：我选择的 CSP 计划中的特定合作伙伴

如果您的报价当前是**选项 2：我选择的 CSP 计划中的特定合作伙伴**，您希望导航到**选项 1：CSP 计划中的任何合作伙伴**，请使用以下说明创建请求：

1. 转到["支持请求"页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单会自动为您填写。

   > [!NOTE]
   > 不要更改预填充的下拉菜单选择。

2. 要**选择产品版本**，请选择 **"实时产品/服务管理**"。
3. 对于**选择最能描述问题的类别**，请选择引用您的报价的类别。
4. 对于**选择最能描述问题的问题**，请选择 **"更新现有产品/服务**"。
5. 选择 **"下一步**"以定向到 **"问题详细信息"页面**，以了解有关您的问题的更多详细信息。
6. 使用**取消授权 CSP**作为问题标题并填写所需的其余部分。

 如果您的报价目前是选项**2：我选择的 CSP 计划中的特定合作伙伴**，并且您希望导航到**选项 3：CSP 计划中没有合作伙伴**，则只有在您以前授权的 CSP 计划中合作伙伴没有将您的报价转售给最终客户时，您才能导航到该选项。 请使用以下说明创建请求：

1. 转到["支持请求"页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单会自动为您填写。

   > [!NOTE]
   > 不要更改预填充的下拉菜单选择。

2. 要**选择产品版本**，请选择 **"实时产品/服务管理**"。
3. 对于**选择最能描述问题的类别**，请选择引用您的报价的类别。
4. 对于**选择最能描述问题的问题**，请选择 **"更新现有产品/服务**"。
5. 选择 **"下一步**"以定向到 **"问题详细信息"页面**，以了解有关您的问题的更多详细信息。
6. 使用**取消授权 CSP**作为问题标题并填写所需的其余部分。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>从选项 3 导航：CSP 计划中没有合作伙伴

如果您的报价当前是**选项 3：CSP 计划中没有合作伙伴**，您可以随时导航到其他两个选项之一。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>与 CSP 计划中的合作伙伴共享销售和支持材料

为了帮助云解决方案提供商计划中的合作伙伴最有效地代表您的产品并与您的组织互动，您必须提交可供经销商使用的销售和支持材料。 这些资源不会暴露给市场网店中的客户。

### <a name="partner-center-csp-channel"></a>合作伙伴中心 CSP 通道

如果您已选择加入合作伙伴中心的 CSP 渠道，则发布商必须输入一个 URL，该 URL 在产品/服务列表模块下承载相关营销材料和将联系信息引入 CSP 渠道：

![合作伙伴中心 CSP 抵押品信息](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>云合作伙伴门户 CSP 渠道

如果您已选择加入云合作伙伴门户中的 CSP 渠道，则发布者必须输入一个 URL，该 URL 承载相关的营销材料并将联系信息引入 CSP 渠道：

![云合作伙伴门户 CSP 抵押品信息](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>后续步骤

访问[Azure 应用商店和应用程序源发布者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

要了解有关市场 GTM 服务的信息，请参阅[上市服务](https://partner.microsoft.com/reach-customers/gtm)。

登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)或[云合作伙伴门户](https://cloudpartner.azure.com/)以创建和配置您的产品/服务。
