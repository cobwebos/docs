---
title: "Azure Active Directory B2C：切换到 B2C 租户 | Microsoft Docs"
description: "如何切换到 Active Directory B2C 租户的上下文"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 40d8d57d974a949fbdc0a06eeceb2d06bfbaa09f
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---

# <a name="switching-to-your-azure-ad-b2c-tenant"></a>切换到 Azure AD B2C 租户

若要配置 Azure AD B2C，需处于 Azure AD B2C 租户的上下文中。

## <a name="log-into-azure-ad-b2c-tenant"></a>登录到 Azure AD B2C 租户

若要导航到 Azure AD B2C 租户，必须以 Azure AD B2C 租户全局管理员身份登录到 Azure 门户。

1. 登录到 [Azure 门户](http://portal.azure.com)。
1. 单击右上角的电子邮件地址或图片即可切换租户。
1. 在显示的 `Directory` 列表中，选择要管理的 Azure AD B2C 租户。

Azure 门户将刷新。  现在，你已登录到 Azure AD B2C 租户上下文中的 Azure 门户。

## <a name="navigate-to-the-b2c-features-blade"></a>导航到 B2C 功能边栏选项卡

1. 单击左侧导航上的“浏览”。
1. 单击“更多服务”，然后在左侧导航窗格中搜索 `Azure AD B2C`。  （若要固定到左侧启动板，请单击 Azure AD B2C 左侧的星号）
1. 单击“Azure AD B2C”访问 B2C 功能边栏选项卡。
   
    ![浏览到 B2C 功能边栏选项卡的屏幕截图](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> 你需要是能够访问 B2C 功能边栏选项卡的 B2C 租户全局管理员。 任何其他租户的全局管理员或任何租户的用户均不能访问它。  可以通过 Azure 门户右上角的租户切换器切换到 B2C 租户。

