---
title: 云解决方案提供商 |Azure Marketplace
description: 发布者现在可以通过 Microsoft 云解决方案提供商（CSP）合作伙伴渠道销售其产品/服务。
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038688"
---
# <a name="cloud-solution-providers"></a>云解决方案提供商

软件产品/服务可与云解决方案提供商（CSP）计划中的合作伙伴提供的数百万个合格 Microsoft 客户，以及通过[Microsoft web 商店](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)提供的公开上市。

发布者可根据选择来配置 CSP 计划中的可用性，为新产品/服务或现有产品/服务提供产品/服务，使合作伙伴能够销售你的产品并为客户创建捆绑式解决方案。

发布者负责向最终用户提供中断修复支持，并为 CSP 计划中的合作伙伴提供一种机制，并/或让客户与你联系以获得支持。 最佳做法是，在 CSP 计划中为合作伙伴提供用户文档、培训和服务运行状况/中断通知（如果适用），以使 CSP 计划中的合作伙伴能够处理来自客户的第1层支持请求。  

以下产品/服务有资格在 CSP 计划中选择加入合作伙伴：

- 软件即服务（SaaS）服务提供
- 虚拟机 (VM)
- 解决方案模板
- 托管应用程序

> [!NOTE]
> 默认情况下，"容器" 和 "自带许可证（BYOL）" VM Sku 选择加入 CSP 计划中的合作伙伴。

## <a name="how-to-configure-an-offering"></a>如何配置产品

CSP 计划选择加入设置在合作伙伴中心或云合作伙伴门户产品/服务创建体验中进行配置。 [了解有关更改发布者体验的详细信息](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>合作伙伴中心选择加入

在合作伙伴中心，你会发现 "CSP 分销商受众" 模块下有选择体验。

![CSP 分销商受众](media/marketplace-publishers-guide/csp-reseller-audience.png)

在 CSP 经销商的受众模块中，你有三个选项可供选择：

- 选项一： CSP 计划中的任何合作伙伴
- 选项2：我选择的 CSP 程序中的特定合作伙伴
- 选项3： CSP 计划中没有合作伙伴

#### <a name="option-one-any-partner-in-the-csp-program"></a>选项一： CSP 计划中的任何合作伙伴

![CSP 计划中的任何合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 通过选择此选项，CSP 计划中的所有合作伙伴都有资格向其客户转售你的产品/服务。

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>选项2：我选择的 CSP 程序中的特定合作伙伴

![我选择的 CSP 程序中的特定合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

通过选择此选项，可以授权 CSP 计划中的哪些合作伙伴有资格转售你的产品/服务。

若要授权合作伙伴，请单击 "**选择 CSP 合作伙伴**"，此时将显示一个菜单，让你按伙伴名称或 CSP AZURE ACTIVE DIRECTORY （AAD）租户 ID 进行搜索。

![选择 CSP 菜单](media/marketplace-publishers-guide/csp-pop-up-module.png)

您可以应用搜索筛选器，例如**国家/地区**、**资格**或**技能**。

![合作伙伴搜索的国家/地区、资格和技能筛选器](media/marketplace-publishers-guide/csp-add-resellers.png)

选择伙伴列表后，选择 "**添加**"。

![CSP 计划中授权合作伙伴的示例列表](media/marketplace-publishers-guide/csp-add-resellers-details.png)

此时会显示一个表，其中显示了所选合作伙伴的列表。

![具有 CSP 分销商观众页面上合作伙伴列表的表](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

选择 "**保存草稿**" 以注册更改。

如果未发布此产品/服务，则需要发布产品/服务，使其可供所选合作伙伴使用。

>[!NOTE]
>如果在给定区域的 CSP 计划中授权合作伙伴，则他们可以将该产品/服务销售给属于该特定区域的任何客户。 请参阅[云解决方案提供商计划地区市场和货币](https://docs.microsoft.com/partner-center/regional-authorization-overview)，详细了解如何在区域下对 CSP 产品/服务进行分类。

如果要更新已发布产品/服务的 CSP 列表，请添加其他合作伙伴，并选择 "**同步 CSP 受众**"。

如果有一个产品/服务已获得授权合作伙伴列表，并且想要为另一个产品/服务使用同一列表，请使用**导入/导出**。 导航到具有 CSP 列表的产品/服务，并选择 "**导出 csp**"。 该函数开发可以导入到另一个产品/服务的 .csv 文件。

#### <a name="option-three-no-partners-in-the-csp-program"></a>选项3： CSP 计划中没有合作伙伴

![CSP 计划中没有合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

通过选择此选项，你将选择退出 CSP 计划。 你可以随时更改此选择。

### <a name="cloud-partner-portal-opt-in"></a>云合作伙伴门户选择加入

在云合作伙伴门户中，在 Marketplace 或店面选项卡上设置了选择加入。在 CSP 计划中选择特定合作伙伴的能力仅在合作伙伴中心可用。

![CPP 中的 CSP 选择体验](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP 计划中的 Deauthorize 合作伙伴

如果已在 CSP 计划中授权合作伙伴，并且该合作伙伴已将产品转售给给客户，则不允许 deauthorize 该合作伙伴。

如果 CSP 计划中的合作伙伴未向客户销售产品，并且你想要在发布产品/服务后删除 CSP，请使用以下说明：

1. 请参阅[支持请求页面](https://aka.ms/marketplacepublishersupport)。 将自动为您填充前几个下拉菜单。

   > [!NOTE]
   > 请勿更改预填充的下拉菜单选项。

2. 对于 "**选择产品版本**"，请选择 " **Live 产品管理**"。
3. 对于 **"选择最能描述此问题的类别**"，请选择引用您的产品/服务的类别。
4. 对于 **"选择最能说明问题的问题**"，请选择 "**更新现有产品/服务**"。
5. 选择 "**下一步**" 以转到 "**问题详细信息" 页**，输入有关问题的更多详细信息。
6. 使用**DEAUTHORIZE CSP**作为问题标题，并填写其余必需部分。




## <a name="navigate-between-options"></a>在选项之间导航

使用此部分在三个 CSP 分销商选项之间导航。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>从选项 one 导航： CSP 计划中的任何合作伙伴

如果产品/服务当前为**选项1： CSP 计划中的任何合作伙伴**，并且你想要导航到其他两个选项中的任何一个选项，请使用以下说明创建请求：

1. 请参阅[支持请求页面](https://aka.ms/marketplacepublishersupport)。 将自动为您填充前几个下拉菜单。

   > [!NOTE]
   > 请勿更改预填充的下拉菜单选项。

2. 对于 "**选择产品版本**"，请选择 " **Live 产品管理**"。
3. 对于 **"选择最能描述此问题的类别**"，请选择引用您的产品/服务的类别。
4. 对于 **"选择最能说明问题的问题**"，请选择 "**更新现有产品/服务**"。
5. 选择 "**下一步**" 以转到 "**问题详细信息" 页**，输入有关问题的更多详细信息。
6. 使用**DEAUTHORIZE CSP**作为问题标题，并填写其余必需部分。

> [!NOTE]
> 如果你正在尝试导航到选项2，并且 CSP 计划中的一个合作伙伴已将该产品/服务转售给给其客户，则默认情况下该合作伙伴已在你的授权合作伙伴列表中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>从 "选项2：选择 CSP 计划中的特定合作伙伴" 导航

如果产品/服务当前为**选项2：选择 csp 程序中的特定合作伙伴**，并希望导航到**选项 one： csp 计划中的任何合作伙伴**，请使用以下说明创建请求：

1. 请参阅[支持请求页面](https://aka.ms/marketplacepublishersupport)。 将自动为您填充前几个下拉菜单。

   > [!NOTE]
   > 请勿更改预填充的下拉菜单选项。

2. 对于 "**选择产品版本**"，请选择 " **Live 产品管理**"。
3. 对于 **"选择最能描述此问题的类别**"，请选择引用您的产品/服务的类别。
4. 对于 **"选择最能说明问题的问题**"，请选择 "**更新现有产品/服务**"。
5. 选择 "**下一步**" 以转到 "**问题详细信息" 页**，输入有关问题的更多详细信息。
6. 使用**DEAUTHORIZE CSP**作为问题标题，并填写其余必需部分。

 如果 "产品/服务 **" 为 "选项2： csp 计划中的特定合作伙伴"** ，并希望导航到 "**选项3： csp 计划中的任何合作伙伴**"，并且你之前获得授权的 csp 计划中的合作伙伴未将你的产品/服务转售给给最终客户，则只能导航到该选项。 请按照以下说明创建请求：

1. 请参阅[支持请求页面](https://aka.ms/marketplacepublishersupport)。 将自动为您填充前几个下拉菜单。

   > [!NOTE]
   > 请勿更改预填充的下拉菜单选项。

2. 对于 "**选择产品版本**"，请选择 " **Live 产品管理**"。
3. 对于 **"选择最能描述此问题的类别**"，请选择引用您的产品/服务的类别。
4. 对于 **"选择最能说明问题的问题**"，请选择 "**更新现有产品/服务**"。
5. 选择 "**下一步**" 以转到 "**问题详细信息" 页**，输入有关问题的更多详细信息。
6. 使用**DEAUTHORIZE CSP**作为问题标题，并填写其余必需部分。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>从选项3导航： CSP 计划中没有合作伙伴

如果产品/服务当前为**选项3： CSP 计划中没有合作伙伴**，则随时可以导航到其他两个选项中的任意一个。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>在 CSP 计划中与合作伙伴共享销售和支持材料

为了帮助云解决方案提供商计划中的合作伙伴最有效地表示您的产品/服务并与您的组织合作，您必须提交销售商提供的销售和支持材料。 这些资源不会公开给 marketplace 商店中的客户。

### <a name="partner-center-csp-channel"></a>合作伙伴中心 CSP 通道

如果你已在合作伙伴中心选择了 CSP 通道，则发布者必须输入一个 URL，该 URL 将相关营销材料和渠道联系信息托管到产品/服务列表模块下的 CSP 通道：

![合作伙伴中心 CSP 宣传品信息](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>云合作伙伴门户 CSP 通道

如果你已在云合作伙伴门户中选择了 CSP 通道，则发布者必须输入一个 URL，该 URL 将相关营销材料和渠道联系信息托管到 CSP 通道：

![云合作伙伴门户 CSP 宣传品信息](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>后续步骤

请访问[Azure Marketplace 和 AppSource 发布者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

若要了解有关 marketplace 中转服务的详细信息，请参阅[市场推广服务](https://partner.microsoft.com/reach-customers/gtm)。

登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)或[云合作伙伴门户](https://cloudpartner.azure.com/)来创建和配置产品/服务。
