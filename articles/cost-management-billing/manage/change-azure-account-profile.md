---
title: 更改 Azure 帐户的联系人信息
description: 介绍如何更改 Azure 管理帐户的联系人信息
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 08/03/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb7978dfa2c3ded2ae8e8c621bcb679f19620a2c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553472"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>更改 Azure 帐户的联系人信息

本文可帮助你在 Azure 门户中更新帐户的联系人信息。 更新联系人信息的说明会因计费帐户类型而异。 若要详细了解计费帐户并确定自己的计费帐户类型，请参阅[在 Azure 门户中查看计费帐户](view-all-accounts.md)。

买方地址 - 买方地址是对计费帐户负责的组织或个人的地址以及联系信息。 该地址会显示在对该计费帐户生成的所有发票上。

帐单寄往地址 - 帐单寄往地址是负责处理为计费帐户生成的发票的组织或个人的地址以及联系信息。 Microsoft Online Service 计划 (MOSP) 的计费帐户会有一个帐单寄往地址，该地址显示在对该帐户生成的所有发票上。 对于 Microsoft 客户协议 (MCA) 的计费帐户，每个计费对象信息都有一个帐单寄往地址，并且该地址会显示在为计费对象信息生成的发票中。

用于接收服务和市场营销电子邮件的联系人电子邮件地址 - 你可以指定一个不同于登录时所用的电子邮件地址来接收有关 Azure 帐户的重要计费、服务和推荐通知。 服务通知电子邮件（例如紧急安全问题、价格更改或对你的帐户正在使用的服务的中断性变更）始终会发送到你的登录地址。

## <a name="update-an-mosp-billing-account-address"></a>更新 MOSP 计费帐户地址

1. 使用具有帐户的帐户管理员权限的电子邮件地址登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索“成本管理 + 计费”。  
    ![显示如何在门户中搜索“成本管理 + 计费”的屏幕截图](./media/change-azure-account-profile/search-cmb.png)
1. 在左侧选择“属性”。  
    ![显示更新地址页的屏幕截图](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. 选择“更新帐单邮寄地址”以更新销售对象地址和付款人地址。 输入新的地址，然后选择“保存”。  
    ![显示更新地址页的屏幕截图](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>更新 MCA 计费帐户买方地址

1. 使用具有 Microsoft 客户协议计费帐户所有者或参与者角色的电子邮件地址登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索“成本管理 + 计费”。  
    ![显示如何在门户中搜索“成本管理 + 计费”的屏幕截图](./media/change-azure-account-profile/search-cmb.png)
1. 选择左侧的“属性”，然后选择“更新买方”。  
    ![显示选择更新买方的屏幕截图](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. 输入新的地址并选择“保存”。  
    ![显示更新地址的屏幕截图](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > 某些帐户需要进行额外验证才能更新买方。 如果你的帐户需要手动批准流程，系统会要求你联系 Azure 支持部门。

## <a name="update-an-mca-billing-account-address"></a>更新 MCA 计费帐户地址

1. 使用具有 MCA 计费帐户或计费对象信息的所有者或参与者角色的电子邮件地址登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索“成本管理 + 计费”。  
    ![显示如何在门户中搜索“成本管理 + 计费”的屏幕截图](./media/change-azure-account-profile/search-cmb.png)
1. 在左侧选择“计费对象信息”。
1. 选择计费对象信息以更新帐单邮寄地址。  
    ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. 在左侧选择“属性”。
1. 选择“更新地址”。  
    ![显示如何在门户中搜索“成本管理 + 计费”的屏幕截图](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. 输入新的地址，然后选择“保存”。  
    ![显示更新地址的屏幕截图](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>服务和营销电子邮件

系统每隔 90 天就会在 [Azure 门户](https://portal.azure.com)中提示你验证或更新电子邮件地址。 Microsoft 向该电子邮件地址发送包含 Azure 帐户相关信息的电子邮件是出于以下目的：

- 服务通知
- 安全警报
- 计费目的
- 支持
- 营销通信
- 基于 Azure 使用情况提供的最佳做法建议

输入用于接收帐户通信的电子邮件地址。 输入电子邮件地址即表示你选择从 Microsoft 接收通信。

![提示更新联系人信息的示例](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>更改联系人电子邮件地址

可以使用以下任一方法更改联系人电子邮件地址。 更新联系人电子邮件地址不会更新用于登录的电子邮件地址。

* 如果你是 MOSP 帐户的帐户管理员，请按照[更新 MOSP 计费帐户地址](#update-an-mosp-billing-account-address)中的说明进行操作，并在最后一个步骤中选择“更新联系人信息”。 然后输入新的电子邮件地址。

* 转到 Azure 门户中的[联系人信息](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade)区域，然后输入新的电子邮件地址。 

* 在 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade)中，选择带有你的姓名缩写或图像的图标。 然后，选择上下文菜单 ( **...** )。接下来，从菜单中选择“我的联系人信息”，然后输入新的电子邮件地址。

![在 Azure 中更新电子邮件地址的示例](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>选择退出营销电子邮件

如果不希望接收营销电子邮件，请执行以下操作：

1. 转到[请求表单](https://account.microsoft.com/profile/permissions-link-request)，使用个人资料电子邮件地址提交一项请求。 你会收到电子邮件发送的链接，可以通过它更新首选项。
1. 选择该链接，打开“管理通信权限”页。 该页显示电子邮件地址选择加入的营销通信类型。 清除要选择退出的任何选择，然后选择“保存”。  
    ![用于管理通信权限的页面的示例](./media/change-azure-account-profile/manage-communication-permissions.png)

选择退出营销通信后，你仍然会收到服务通知，具体取决于你的帐户。

## <a name="update-the-email-address-that-you-sign-in-with"></a>更新用于登录的电子邮件地址

你无法更新用于访问帐户的电子邮件地址。 但是，如果你有一个 MOSP 计费帐户，则可以使用新的电子邮件地址注册另一个帐户，并将订阅的所有权转移到这个新的帐户。 [可以为 MCA 计费帐户提供新的电子邮件地址权限](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="update-your-credit-card"></a>更新信用卡

若要了解如何更新信用卡，请参阅[更改用于支付 Azure 订阅的信用卡](change-credit-card.md)。

## <a name="update-your-country-or-region"></a>更新国家或地区

不支持更改现有帐户的国家或地区。 但是，你可以在不同的国家或地区创建新帐户，然后联系 Azure 支持部门，将订阅转移到新帐户。

## <a name="change-the-subscription-name"></a>更改订阅名称

1. 登录到 [Azure 门户](https://portal.azure.com)，在左窗格中选择“订阅”，然后选择要重命名的订阅。
1. 选择“概览”，然后从命令栏中选择“重命名”。   
    ![重命名 Azure 订阅的示例](./media/change-azure-account-profile/rename-sub.png)
1. 更改名称之后，请选择“保存”。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看计费帐户](view-all-accounts.md)
