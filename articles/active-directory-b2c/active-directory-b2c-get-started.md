---
title: "Azure Active Directory B2C：创建 Azure Active Directory B2C 租户 | Microsoft Docs"
description: "有关如何创建 Azure Active Directory B2C 租户的主题"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: 77b8e707b5743ce2d0b3eb52f75828c9ab0337d9


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C：创建 Azure AD B2C 租户
若要开始使用 Microsoft Azure Active Directory (Azure AD) B2C，请按照本文中所述的三个步骤。

## <a name="step-1-sign-up-for-an-azure-subscription"></a>步骤 1：注册 Azure 订阅
如果你已有 Azure 订阅，则可以跳过此步骤。 如果没有，请注册 [Azure 订阅](../active-directory/sign-up-organization.md)并获取 Azure AD B2C 的访问权限。

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>步骤 2：创建 Azure AD B2C 租户
按照下列步骤创建一个新的 Azure AD B2C 租户。 目前无法在现有的租户中打开 B2C 功能。

1. 以订阅管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com/)。 这是与你在注册 Azure 时使用的同一个工作或学校帐户，或同一个 Microsoft 帐户。
2. 单击“新建” > “应用程序服务” > “Active Directory” > “目录” > “自定义创建”。
   
    ![开始创建租户的屏幕截图](./media/active-directory-b2c-get-started/new-directory.png)
3. 为你的租户选择“名称”、“域名”和“国家或地区”。
4. 选中“这是一个 B2C 目录”选项。
5. 单击复选标记以完成操作。
   
    ![创建 B2C 目录的复选标记的屏幕截图](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. 你的租户现已创建，并将显示在“Active Directory”扩展中。 同时你也成为了租户的全局管理员。 可以根据需要添加其他全局管理员。
   
   > [!IMPORTANT]
   > 如果计划将 B2C 租户用于生产应用，请阅读[生产规模与预览 B2C 租户](active-directory-b2c-reference-tenant-type.md)一文。 请注意，当删除现有 B2C 租户并重新创建具有相同域名的 B2C 租户时，存在已知问题。 你必须创建具有不同域名的 B2C 租户。
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>步骤 3：导航到 Azure 门户上的 B2C 功能边栏选项卡
1. 导航到左侧导航栏上的 Active Directory 扩展。
2. 在“目录”选项卡下找到你的租户，然后单击。
3. 单击“配置”  选项卡。
4. 单击“B2C 管理”部分中的“管理 B2C 设置”链接。
   
    ![B2C 目录配置的屏幕截图](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. 将在新浏览器选项卡或窗口中打开带有 B2C 功能边栏选项卡的 Azure 门户。
   
   > [!IMPORTANT]
   > 租户可能需要 2 - 3 分钟才能访问 Azure 门户。 在一段时间后重试上述步骤以解决此问题。 如果仍未解决，请联系“支持”。
   > 
   > 
6. 将此边栏选项卡固定到启动板，以方便访问。 （引导工具在功能边栏选项卡的右上角用红色标记。）
   
    ![B2C 功能边栏选项卡的屏幕截图](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > 你可以在 [Azure 经典门户](https://manage.windowsazure.com/)上管理用户和组、自助服务密码重置配置以及租户的公司品牌功能。
   > 
   > 

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>步骤 4：将 Azure AD B2C 租户链接到 Azure 订阅
如果打算为生产应用使用 B2C 租户，需将 Azure AD B2C 租户链接到 Azure 订阅以支付使用费。 请阅读[此文](active-directory-b2c-how-to-enable-billing.md)了解具体操作。

   > [!IMPORTANT]
   > 如果未将 Azure AD B2C 租户链接到 Azure 订阅，Azure 门户中的 B2C 功能边栏选项卡上会显示一条警告消息（“未将任何订阅链接到此 B2C 租户，或者订阅需要关注。”）。 在将应用转移到生产环境之前，必须执行此步骤。
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>轻松访问 Azure 门户上的 B2C 功能边栏选项卡
为了提高可发现性，我们在 Azure 门户上添加了 B2C 功能边栏选项卡的快捷方式。

1. 以 B2C 租户的全局管理员身份登录 Azure 门户。 如果你已登录不同的租户，请（在右上角）切换租户。
2. 单击左侧导航上的“浏览”。
3. 单击“Azure AD B2C”访问 B2C 功能边栏选项卡。
   
    ![浏览到 B2C 功能边栏选项卡的屏幕截图](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>后续步骤
通过阅读 [Azure Active Directory B2C：注册应用程序](active-directory-b2c-app-registration.md)，了解如何通过 Azure AD B2C 注册应用程序并构建快速启动应用程序。




<!--HONumber=Jan17_HO4-->


