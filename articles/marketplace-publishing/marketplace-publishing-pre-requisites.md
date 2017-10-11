---
title: "为 Azure 应用商店创建产品/服务的非技术先决条件 | Microsoft Docs"
description: "了解创建产品/服务并将其部署到 Azure 应用商店以供其他人购买的要求。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ms.openlocfilehash: 4f86d444a2f2b97fd8605d480db358813bc39fd3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>为 Azure 应用商店创建产品/服务的一般先决条件
了解推进其他产品/服务过程所需的一般、以业务流程为中心的先决条件。

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>确保向 Microsoft 注册为卖家
有关向 Microsoft 注册卖家帐户的详细说明，请转到[帐户创建和注册](marketplace-publishing-accounts-creation-registration.md)。

* 
            **如果公司已在开发人员中心中注册为卖家，并且希望创建新的产品/服务**，则使用完成开发人员中心注册所使用的相同电子邮件 ID 登录到发布门户。 为了使开发人员中心和发布门户相互链接，必需执行此步骤。
* 
            **如果公司已在开发人员中心中注册为卖家，并且希望编辑现有产品/服务**，则使用管理员帐户或在发布门户中添加为共同管理员的帐户登录到发布门户。 下面提供了添加共同管理员帐户的步骤。

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>在发布门户中添加共同管理员的步骤
发布门户的管理员可添加公司的其他成员，这些成员在发布门户中以共同管理员身份处理应用程序。 **假设你是管理员，**下面给出是的步骤添加共同管理员。

> [!NOTE]
> 对于新用户，在发布门户中添加共同管理员之前，请确保已在发布门户中创建至少一个应用程序。 这是必需的，因为“发布者”选项卡仅在发布门户中创建至少一个应用程序后显示。
> 
> 

1. 确保共同管理员电子邮件 ID 为 Microsoft 帐户 (MSA)。 如果不是，请使用此[链接](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)将其注册为 MSA。
2. 在尝试添加共同管理员之前，请确保管理员帐户下至少有一个应用程序。
3. 完成上述步骤后，使用共同管理员帐户登录到发布门户，并注销。
4. 现在，使用管理员电子邮件 ID 登录到发布门户。
5. 导航到“发布者”->依次选择帐户->“管理员”->“添加共同管理员”（下面提供了屏幕截图）
   
    ![绘制](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. 确保监视在发布过程的各个阶段（例如开发人员中心、发布门户）中提供的电子邮件 ID 是否收到任何来自 Microsoft 的通信。
7. 对于开发人员中心注册，请避免使用与个人相关联的帐户。 建议这样做是为了消除对单个个人的依赖性。
8. 如果遇到任何开发人员中心注册问题，请使用此[链接](https://developer.microsoft.com/en-us/windows/support)提出票证。

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>在发布门户中删除共同管理员的步骤
**假设你是管理员，**下面给出是的步骤删除共同管理员。

1. 使用管理员电子邮件 ID 登录到发布门户。
2. 导航到“发布者”->依次选择帐户->“管理员” -> “共同管理员”。
3. 单击要删除的共同管理员旁边的“X”按钮（下面提供了屏幕截图）。
   
    ![绘制](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> 如果计划仅发布免费产品/服务（或自带许可证），则无需完成公司税务和银行信息。
> 
> 必须完成公司注册才能开始。 但是，当公司在 Microsoft 开发人员帐户中处理税务和银行信息时，开发人员可以开始在[发布门户](https://publish.windowsazure.com)中着手创建虚拟机映像、使其获得认证，并在 Azure 过渡环境中测试它。 仅在向 Azure 应用商店发布产品/服务的最后一步中需要完整的卖家帐户批准。
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>获取“即付即用”订阅
这是用于创建 VM 映像并将这些映像分发到 [Azure 应用商店](https://azure.microsoft.com/marketplace/)的订阅。 如果没有现有订阅，请在 https://account.windowsazure.com/signup?offer=ms-azr-0003p 注册。

## <a name="sell-from-countries"></a>“销售”国家/地区
> [!WARNING]
> 若要在 Azure 应用商店销售服务，必须确保注册实体来自已批准的“销售”国家/地区之一。 此限制是出于付款和税务的原因。 我们正在积极地寻求在不久的将来扩展这一国家/地区列表，敬请关注。 有关完整列表，请参阅 [Azure 应用商店参与策略](http://go.microsoft.com/fwlink/?LinkID=526833)的第 1b 部分。
> 
> 

## <a name="next-steps"></a>后续步骤
完成非技术先决条件后，接下来是特定于产品/服务的技术先决条件。 单击指向要为 Azure 应用商店创建的各个产品/服务类型的相关文章的链接。

* [VM 技术先决条件](marketplace-publishing-vm-image-creation-prerequisites.md)
* [解决方案模板技术先决条件](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure 应用商店](marketplace-publishing-getting-started.md)

