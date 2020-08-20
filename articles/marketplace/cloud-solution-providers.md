---
title: 云解决方案提供商 - Microsoft 商业市场
description: 了解如何通过商业市场中的 Microsoft 云解决方案提供商 (CSP) 计划合作伙伴渠道销售产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: 51f4c9a5748896eff9e9f3ae9719b2e7e7cdd4e7
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607477"
---
# <a name="cloud-solution-provider-program"></a>云解决方案提供商计划

本文介绍如何配置要在云解决方案提供商 (CSP) 计划中提供的产品/服务。 除了通过 [商业 marketplace 在线商店](comparing-appsource-azure-marketplace.md)发布你的产品/服务，你还可以通过 CSP 计划进行销售，以便向该计划提供的数百万个合格 Microsoft 客户。

可以按照“选择加入”的原则配置要在 CSP 计划中提供的新的或现有的产品/服务，以允许 CSP 合作伙伴销售你的产品并为客户创建捆绑式解决方案。

发布者负责向最终客户提供中断后修复支持，并提供一个机制来让 CSP 计划中的合作伙伴和/或客户与你联系以获得支持。 最佳做法是为 CSP 计划中的合作伙伴提供用户文档、培训和服务运行状况/中断通知（如果适用），使 CSP 计划中的合作伙伴具备处理客户提出的一级支持请求的能力。  

可以选择让 CSP 计划中的合作伙伴销售以下产品/服务：

- 软件即服务 (SaaS) 事务产品/服务
- 虚拟机 (VM)
- 解决方案模板
- 托管应用程序

> [!NOTE]
> 默认情况下，容器和自带许可证 (BYOL) VM 计划选择加入到 CSP 计划中的合作伙伴销售。

## <a name="how-to-configure-an-offer"></a>如何配置产品/服务

在合作伙伴中心创建产品/服务时，配置 CSP 计划选择加入。 [详细了解发布者体验的变化](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>在合作伙伴中心选择加入

选择体验位于 "CSP 分销商受众" 模块下：

![CSP 经销商受众](media/marketplace-publishers-guide/csp-reseller-audience.png)

从以下三个选项中进行选择：

1. CSP 计划中的任何合作伙伴。
2. 我选择的 CSP 程序中的特定合作伙伴。
3. CSP 计划中没有合作伙伴。

#### <a name="option-1-any-partner-in-the-csp-program"></a>选项1： CSP 计划中的任何合作伙伴

![CSP 计划中的任何合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 如果选择此选项，CSP 计划中的所有合作伙伴都有资格向其客户转售你的产品/服务。

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>选项2：我选择的 CSP 程序中的特定合作伙伴

![我选择的 CSP 计划中的特定合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

如果选择此选项，需要指定 CSP 计划中有资格转售你的产品/服务的合作伙伴并向其授权。

若要为合作伙伴授权，请单击“选择 CSP 合作伙伴”，此时会显示一个菜单，让你按合作伙伴名称或 CSP Azure Active Directory (AAD) 租户 ID 进行搜索。

![“选择 CSP 合作伙伴”菜单](media/marketplace-publishers-guide/csp-pop-up-module.png)

可以应用搜索筛选器，例如“国家/地区”、“资质”或“技能”。  

![用于搜索合作伙伴的“国家/地区”、“资质”和“技能”筛选器](media/marketplace-publishers-guide/csp-add-resellers.png)

选择合作伙伴列表后，选择“添加”。

![CSP 计划中已授权合作伙伴的示例列表](media/marketplace-publishers-guide/csp-add-resellers-details.png)

“CSP 经销商受众”页上会显示一个表格，其中列出了所选的合作伙伴。

![“CSP 经销商受众”页上包含合作伙伴列表的表格](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

选择“保存草稿”以注册更改。

如果此产品/服务尚未发布，则需要将其发布，以将其提供给所选的合作伙伴。

>[!NOTE]
>如果在给定的区域为 CSP 计划中的某家合作伙伴授权，则该合作伙伴可以向属于该特定区域的任何客户销售该产品/服务。 有关不同区域中 CSP 产品/服务的分类方式的详细信息，请参阅[云解决方案提供商计划区域市场和货币](https://docs.microsoft.com/partner-center/regional-authorization-overview)。

如果要更新已发布产品/服务的 CSP 列表，请添加其他合作伙伴，然后选择“同步 CSP 受众”。

如果某个产品/服务已有一个已授权合作伙伴列表，而你想要对另一个产品/服务使用同一列表，请使用“导入/导出”。 导航到具有 CSP 列表的产品/服务，选择“导出 CSP”。 该功能将制作一个可导入到另一产品/服务的 .csv 文件。

#### <a name="option-3-no-partners-in-the-csp-program"></a>选项3： CSP 计划中没有合作伙伴

![不包括 CSP 计划中的任何合作伙伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

选择此选项表示你选择让产品/服务退出 CSP 计划。 随时可以更改此选择。

## <a name="deauthorize-partners-in-the-csp-program"></a>对 CSP 计划中的合作伙伴取消授权

如果你已为 CSP 计划中的某家合作伙伴授权，并且该合作伙伴已向其客户转售产品，则你无法对该合作伙伴取消授权。

如果 CSP 计划中的合作伙伴未向其客户销售你的产品，而你想要在发布产品/服务后删除 CSP，请参考以下说明：

1. 转到[“支持请求”页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单已自动填写。

   > [!NOTE]
   > 请不要更改预先填充的下拉菜单选择内容。

2. 对于“选择产品版本”，请选择“Live 产品/服务管理”。 
3. 对于“选择最适当地描述了问题的类别”，请选择引用你的产品/服务的类别。
4. 对于“选择最适当地描述了问题的问题”，请选择“更新现有产品/服务”。 
5. 选择“下一步”转到“问题详细信息页”，在其中输入有关问题的更多详细信息。 
6. 使用“取消 CSP 授权”作为问题标题，并填写剩余的必填部分。

## <a name="navigate-between-options"></a>在选项之间导航

使用此部分可在三个 CSP 经销商选项之间导航。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>从“选项 1:CSP 计划中的任何合作伙伴”导航

如果产品/服务当前采用“选项 1:CSP 计划中的任何合作伙伴”，而你想要导航到其他两个选项之一，请参考以下说明来创建请求：

1. 转到[“支持请求”页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单已自动填写。

   > [!NOTE]
   > 请不要更改预先填充的下拉菜单选择内容。

2. 对于“选择产品版本”，请选择“Live 产品/服务管理”。 
3. 对于“选择最适当地描述了问题的类别”，请选择引用你的产品/服务的类别。
4. 对于“选择最适当地描述了问题的问题”，请选择“更新现有产品/服务”。 
5. 选择“下一步”转到“问题详细信息页”，在其中输入有关问题的更多详细信息。 
6. 使用“取消 CSP 授权”作为问题标题，并填写剩余的必填部分。

> [!NOTE]
> 如果你要尝试导航到选项 2，而 CSP 计划中的合作伙伴已向其客户转售产品/服务，则默认情况下，该合作伙伴已包含在你的已授权合作伙伴列表中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>从“选项 2:我选择的 CSP 计划中的特定合作伙伴”导航

如果产品/服务当前采用“选项 2:我选择的 CSP 计划中的特定合作伙伴”，而你想要导航到“选项 1:CSP 计划中的任何合作伙伴”，请参考以下说明来创建请求：

1. 转到[“支持请求”页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单已自动填写。

   > [!NOTE]
   > 请不要更改预先填充的下拉菜单选择内容。

2. 对于“选择产品版本”，请选择“Live 产品/服务管理”。 
3. 对于“选择最适当地描述了问题的类别”，请选择引用你的产品/服务的类别。
4. 对于“选择最适当地描述了问题的问题”，请选择“更新现有产品/服务”。 
5. 选择“下一步”转到“问题详细信息页”，在其中输入有关问题的更多详细信息。 
6. 使用“取消 CSP 授权”作为问题标题，并填写剩余的必填部分。

 如果产品/服务当前采用“选项 2:我选择的 CSP 计划中的特定合作伙伴”，而你想要导航到“选项 3:不包括 CSP 计划中的任何合作伙伴”，则仅当 CSP 计划中已授权的合作伙伴尚未向最终客户转售你的产品/服务时，你才能导航到该选项。 请参考以下说明来创建请求：

1. 转到[“支持请求”页](https://aka.ms/marketplacepublishersupport)。 前几个下拉菜单已自动填写。

   > [!NOTE]
   > 请不要更改预先填充的下拉菜单选择内容。

2. 对于“选择产品版本”，请选择“Live 产品/服务管理”。 
3. 对于“选择最适当地描述了问题的类别”，请选择引用你的产品/服务的类别。
4. 对于“选择最适当地描述了问题的问题”，请选择“更新现有产品/服务”。 
5. 选择“下一步”转到“问题详细信息页”，在其中输入有关问题的更多详细信息。 
6. 使用“取消 CSP 授权”作为问题标题，并填写剩余的必填部分。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>从“选项 3:不包括 CSP 计划中的任何合作伙伴”导航

如果产品/服务当前采用“选项 3:不包括 CSP 计划中的任何合作伙伴”，则你随时可以导航到其他两个选项之一。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>与 CSP 计划中的合作伙伴共享销售和支持材料

若要帮助云解决方案提供商计划中的合作伙伴最有效地表示你的产品/服务并与你的组织合作，你必须提交经销商提供的销售和支持材料。 这些资源不会向在线商店中的客户公开。

### <a name="partner-center-csp-channel"></a>合作伙伴中心 CSP 渠道

如果你已在合作伙伴中心选择加入 CSP 渠道，则发布者必须在产品/服务列表模块中输入一个 URL，其中存放了有关 CSP 渠道的营销材料和渠道联系信息：

![合作伙伴中心 CSP 宣传信息](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>后续步骤

访问 [Azure 市场和 AppSource 发布者指南](marketplace-publishers-guide.md)。

若要详细了解市场 GTM 服务，请参阅[市场推广服务](https://partner.microsoft.com/reach-customers/gtm)。

登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建并配置产品/服务。
