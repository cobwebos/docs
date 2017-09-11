---
title: "创建和注册发布者帐户 | Microsoft Docs"
description: "创建 Microsoft 开发人员帐户的说明，同意后，即可在 Azure Marketplace 中销售各种产品/服务类型。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.translationtype: Human Translation
ms.sourcegitcommit: 125d4d4bc916883e3bb53b9b3b4bf46bbb4bf139
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.contentlocale: zh-cn
ms.lasthandoff: 01/06/2017


---
# <a name="create-a-microsoft-developer-account"></a>创建 Microsoft 开发人员帐户
本文介绍成为 Azure Marketplace 的批准 Microsoft 开发人员的必要帐户创建和注册过程。

## <a name="1-create-a-microsoft-account"></a>1.创建 Microsoft 帐户
若要开始发布过程，需要创建 Microsoft 帐户。 此帐户将用于注册 **Microsoft 开发人员中心**和 **Azure 发布门户**。 对于你的 Azure Marketplace 产品/服务，你只应具有一个 Microsoft 帐户。 它不应该特定于服务或产品。

组成用户名的地址应位于你的域上，并受你的 IT 团队控制。 所有与发布有关的活动都应通过此帐户完成。

> [!WARNING]
> Microsoft 帐户注册不支持“Azure”和“Microsoft”等单词。 若要完成帐户创建和注册过程，请避免使用这些单词。
>
>

### <a name="guidelines-for-company-accounts"></a>公司帐户的准则
创建公司帐户时，如果多个用户将需要通过使用建立该帐户的 Microsoft 帐户登录来访问该帐户，请遵循以下准则。

> [!Important]
> 重要信息：若要允许多个用户访问你的开发人员中心帐户，我们建议使用 Azure Active Directory 为单个用户分配角色，这些用户可以通过使用其个人 Azure AD 凭据登录来访问该帐户。 有关详细信息，请参阅[管理帐户用户](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users)。

* 使用属于公司的域（而不是属于单个人）的电子邮件地址（例如，windowsapps@fabrikam.com）创建 Microsoft 帐户。
* 限制只有尽可能最少数量的开发人员可以访问此 Microsoft 帐户。
* 设置公司电子邮件通讯组列表，其中包括需要访问开发人员帐户的所有人员，并将此电子邮件地址添加到你的安全信息。 在需要的时候，这将允许列表中的所有员工接收安全代码以及管理 Microsoft 帐户的安全信息。 如果设置通讯组列表不可行，则将需要个人电子邮件帐户的所有者可供访问，并在出现提示时（例如，将新的安全信息添加到该帐户时或必须从新设备访问该帐户时）共享安全代码。
* 添加不需要分机且主要团队成员可以访问的公司电话号码。
* 一般情况下，让开发人员使用受信任的设备登录公司的开发人员帐户。 所有主要团队成员应有权访问这些受信任的设备。 这将减少访问帐户时要发送安全代码的需要。
* 如果需要允许从不受信任的电脑访问该帐户，则限制最多只有五个开发人员可以进行该访问。 理想情况下，这些开发人员应从共用同一地理位置和网络位置的计算机访问该帐户。
* 经常在 [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) 中查看公司的安全信息以确保这些信息都是最新的。

应主要从受信任的电脑访问开发人员帐户。 这很关键，因为对每个帐户每周生成的代码数有限制。 它还确保提供最无缝的登录体验。

有关其他开发人员帐户准则和安全性的详细信息，请单击[此处](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)。

### <a name="instructions"></a>说明
1. 打开新的 Chrome Incognito 或 Internet Explorer InPrivate 浏览会话，以确保未登录到现有帐户。
2. 使用 [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) 链接，按照上述准则将电子邮件（例如 windowsapp@fabrikam.com)）注册为 Microsoft 帐户。 请根据以下说明操作。

   1. 将你的帐户注册为 Microsoft 帐户期间，需要提供有效电话号码，供系统以文本消息或自动呼叫的形式向你发送帐户验证代码。
   2. 将你的帐户注册为 Microsoft 帐户期间，需要提供有效的电子邮件 ID，以接收用于帐户验证的自动发送电子邮件。
3. 验证已发送到 DL 的电子邮件地址。
4. 现在，可以在 Microsoft 开发人员中心中使用新的 Microsoft 帐户。

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2.在 Microsoft 开发人员中心注册帐户
Microsoft 开发人员中心用于注册一次公司信息。 注册者必须是有效的公司代表，并且必须提供其个人信息（验证身份所用方法）。 注册人必须使用公司共享的 Microsoft 帐户，**并且同一帐户必须在 Azure 发布门户中使用**。 应检查以确保你的公司尚未拥有 Microsoft 开发人员中心帐户，然后再尝试创建该帐户。 在该过程中，我们会收集公司地址信息、银行帐户信息和税务信息。 这些信息通常可从财务或业务联系人处获取。

> [!IMPORTANT]
> 必须填写以下开发人员个人资料，才能进入产品/服务创建和部署的各个阶段。
>
>

| 开发人员个人资料 | 开始起草 | 过渡 | 发布免费模板和解决方案模板 | 发布商业广告 |
| --- | --- | --- | --- | --- |
| 公司注册 |必须具有 |必须具有 |必须具有 |必须具有 |
| 税务配置文件 ID |可选 |可选 |可选 |必须具有 |
| 银行帐户 |可选 |可选 |可选 |必须具有 |

> [!NOTE]
> 自带许可 (BYOL) 仅支持用于虚拟机，并视为**免费**产品。
>
>

### <a name="register-your-company-account"></a>注册公司帐户
1. 打开新的 Internet Explorer InPrivate 或 Chrome Incognito 浏览会话，以确保未登录到个人帐户。
2. 转到 [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)，以将自身注册为开发人员中心中的卖方。 请在继续操作之前，阅读以下重要说明。

   > [!IMPORTANT]
   > 确保将用于在开发人员中心中注册的电子邮件 ID 或通讯组列表（建议使用通讯组列表，以删除个人的依赖性）是首次注册为 Microsoft 帐户。 如果不是，请使用此[链接](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1)进行注册。 此外，**Microsoft 公司域下的任何电子邮件 ID（即 @microsoft.com）不能用于**开发人员中心注册。
   >
   >

    ![绘制][img-signin]
3. 完成“帮助我们保护你的帐户”向导，该向导会通过电话号码或电子邮件地址验证你的身份。

    ![绘制][img-verify]
4. 在“注册帐户信息”部分中，请从下拉菜单中选择“帐户所在国家/地区”。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **“销售”国家/地区：**若要在 Microsoft Azure Marketplace 销售服务，注册实体必须是已批准的上述“销售”国家/地区之一。 此限制是出于付款和税务的原因。 我们正在积极地寻求在不久的将来扩展这一国家/地区列表，敬请关注。 有关详细信息，请参阅 [Marketplace 参与政策](http://go.microsoft.com/fwlink/?LinkID=526833)。
   >
   >
5. 将“帐户类型”选择为“公司”，然后单击“下一步”按钮。

   > [!IMPORTANT]
   > 若要进一步了解帐户类型以及哪一个是你的最佳选择，请查看[帐户类型、位置和费用](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)页
   >
   >

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. 输入“发布者显示名称”，通常为你的公司名称。

   > [!TIP]
   > 你的优惠列出后，开发人员中心中输入的发布者显示名称不会显示在 Azure Marketplace 中。 但若要完成注册过程，必须填写该名称。
   >
   >
7. 输入用于帐户验证的“联系人信息”。

   > [!IMPORTANT]
   > 必须提供准确的联系人信息，因为该信息将用于对你的公司在开发人员中心中获得批准的验证过程。
   >
   >
8. 输入“公司审批者”的联系人信息。 公司审批者是如下人员：可以验证你是否有权代表你的组织在开发人员中心中创建帐户。 完成后，单击“下一步”以转到“付款部分”。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. 输入付款信息，以为你的帐户付款。 如果你有抵扣注册费用的促销代码，可以在此处输入该代码。 否则，请提供信用卡信息（或所在市场支持的 PayPal）。 完成后，单击“下一步”以转到“查看屏幕”。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. 查看你的帐户信息，并确认所有内容都正确无误。 然后，阅读并接受 [Microsoft Azure Marketplace 发布者协议](http://go.microsoft.com/fwlink/?LinkID=699560)的条款和条件。 选中该框，即表明你已阅读并接受这些条款。
11. 单击“完成”，确认你的注册。 我们会向你的电子邮件地址发送一封确认邮件。
12. 如果你打算仅发布免费的产品/服务，请单击“转到 Azure Marketplace 发布门户”，然后可以跳到本文档的第 3 部分（[在发布门户中注册你的帐户](#3-register-your-account-in-the-publishing-portal)）。

如果你打算发布商业产品/服务（例如，带有每小时计费模型的虚拟机产品），请单击“更新帐户信息”，其中必须在你的开发人员中心帐户中填写税务和银行信息。

如果想要以后更新你的税务和银行信息，可以转到下一个部分（即本文档的第 3 部分，[在发布门户中注册你的帐户](#3-register-your-account-in-the-publishing-portal)），稍后通过使用 Azure 发布门户中的链接重新返回。

> [!IMPORTANT]
> 如果是商业产品/服务，在未填写税务和银行帐户信息的情况下，无法将你的产品/服务投入生产。
>
>

如果想要以后更新你的税务和银行信息，可以转到第 3 部分（即[在发布门户中注册你的帐户](#3-register-your-account-in-the-publishing-portal)），稍后通过使用 Azure 发布门户中的链接重新返回。

### <a name="add-tax-and-banking-information"></a>添加税务和银行信息
 如果想要发布供购买的商业产品/服务，还需要在开发人员中心添加付款和税务信息，然后提交该信息进行验证。 如果只发布免费产品/服务（或 BYOL 服务），则不需要添加此类信息。 可以以后添加上述信息，不过验证税务信息需要一些时间。 如果你知道会提供供购买的商业产品/服务，建议你尽快添加上述信息。

**银行信息**

1. 使用你的 Microsoft 帐户登录到 [Microsoft 开发人员中心](http://dev.windows.com/registration?accountprogram=azure)。
2. 单击左侧菜单中的“付款帐户”，在“选择付款方式”下单击“银行帐户”或“PayPal”。

   > [!IMPORTANT]
   > 如果你拥有客户在 Marketplace 购买过的商业产品/服务，该帐户即用于对这些购买进行收款。
   >
   >
3. 输入付款信息，确认无误后单击“保存”。

   > [!IMPORTANT]
   > 如果需要更新或更改你的付款帐户，请按照上述相同步骤操作，以将当前信息替换为新信息。 更改你的付款帐户可能会推迟你的付款（最多一个付款周期）。 此推迟的发生是因为我们需要验证帐户更改，正如你首次设立付款帐户时我们会进行验证。 你的帐户通过验证后，仍会收到全额款项；当前付款周期应得的任何付款将添加到下一个付款周期。
   >
   >
4. 单击“下一步”。

**税务信息**

1. 必要时使用你的 Microsoft 帐户登录到 [Microsoft 开发人员中心](http://dev.windows.com/registration?accountprogram=azure)。
2. 单击左侧菜单中的“税务资料”。
3. 在“设置纳税表单”页上，选择你具有永久居留权的国家或地区，然后选择你保留原籍的国家或地区。 单击“资源组名称” 的 Azure 数据工厂。
4. 输入你的税务详细信息，然后单击“下一步”。

> [!WARNING]
> 在未在你的 Microsoft 开发人员中心帐户中填写税务和银行帐户信息的情况下，将无法投入生成你的商业产品/服务。
>
>

如果在进行开发人员中心注册时遇到问题，请记录支持票证，如下所示

1. 转到支持链接：[https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. 在“与我们联系”部分下，单击“提交事件”按钮（如以下屏幕截图中所示）

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. 选择“开发人员中心帮助”作为“问题类型”，选择“发布和管理应用”作为“类别”。 完成后，单击“启动电子邮件”按钮。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. 系统将提供给你登录页面。 使用任何 Microsoft 帐户登录。 如果没有 Microsoft 帐户，可以使用此[链接](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)创建一个帐户。
5. 填写问题的详细信息，然后通过单击“提交”按钮来提交该票证。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3.在发布门户中注册你的帐户
[发布门户](http://publish.windowsazure.com)用于发布和管理你的产品/服务。

1. 打开新的 Chrome Incognito 或 Internet Explorer InPrivate 浏览会话，以确保未登录到个人帐户。
2. 转到 [http://publish.windowsazure.com](http://publish.windowsazure.com)。
3. 如果你是新用户并且首次登录到发布门户，则必须使用注册你的开发人员中心帐户所用的同一电子邮件 ID 进行登录。 以这种方式将你的开发人员中心帐户和发布门户帐户相互链接。 稍后可以按照以下步骤操作，将公司中使用应用程序的其他有关成员添加为发布门户中的共同管理员。

如果你已添加为发布门户中的共同管理员，则可以使用你的共同管理员帐户进行登录。

> [!TIP]
> [Azure 网站](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)上对这些参与政策进行了介绍。
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4.在发布门户中添加共同管理员的步骤
**假设你是管理员，**下面提供了添加共同管理员的步骤。

> [!NOTE]
> **对于新用户，**在发布门户中添加共同管理员之前，请确保已在发布门户中创建至少一个应用程序。 这是必需的，因为“发布者”选项卡仅在发布门户中创建至少一个应用程序后显示。
>
>

1. 确保共同管理员电子邮件 ID 为 Microsoft 帐户 (MSA)。 如果不是，请使用此[链接](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)将其注册为 MSA。
2. 在尝试添加共同管理员之前，请确保管理员帐户下至少有一个应用程序。
3. 完成上述步骤后，使用共同管理员帐户登录到发布门户，然后注销。
4. 现在，使用管理员电子邮件 ID 登录到发布门户。
5. 导航到“发布者”->依次选择你的帐户->“管理员”->“添加共同管理员”（下面提供了屏幕截图）

   ![绘制](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5.在发布门户中删除共同管理员的步骤
**假设你是管理员，**下面提供了删除共同管理员的步骤。

1. 使用管理员电子邮件 ID 登录到发布门户。
2. 导航到“发布者”->依次选择你的帐户->“管理员” -> “共同管理员”。
3. 单击要删除的共同管理员旁边的“X”按钮（下面提供了屏幕截图）。

    ![绘制](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>后续步骤
现在，你的帐户已完成创建和注册，请确保通过查看[非技术先决条件](marketplace-publishing-pre-requisites.md)，来实现或满足发布你的产品/服务的所有非技术先决条件。

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md

