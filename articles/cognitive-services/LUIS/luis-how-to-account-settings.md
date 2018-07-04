---
title: 在 LUIS 中管理帐户设置 | Microsoft Docs
description: 使用 LUIS 网站管理帐户设置。
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749957"
---
# <a name="manage-your-luis-account"></a>管理 LUIS 帐户
LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 登录信息在 [account.microsoft.com](https://account.microsoft.com) 上进行管理。 创作密钥在 [LUIS][LUIS] 网站的“设置”页面上进行管理。 

## <a name="authoring-key"></a>创作密钥

使用“设置”页面上的这个区域特定的单个创作密钥，可通过 [LUIS][LUIS] 网站和[创作 API](https://aka.ms/luis-authoring-api) 中创作各种应用。 为方便起见，创作密钥每月可执行[有限](luis-boundaries.md)数量的终结点查询。 

![LUIS 设置页](./media/luis-how-to-account-settings/account-settings.png)

创作密钥可用于你拥有的所有应用以及列为协作者的任何应用。

## <a name="authoring-key-regions"></a>创作密钥区域
创作密钥特定于[创作区域](luis-reference-regions.md#publishing-regions)。 此密钥不可用于其他区域。 

## <a name="reset-authoring-key"></a>重置创作密钥
如果创作密钥遭到泄露，请重置密钥。 可在 [LUIS] 网站的任意应用中重置此密钥。 如果通过创作 API 创作应用，则需要将 `Ocp-Apim-Subscription-Key` 的值更改为新密钥。 

## <a name="delete-account"></a>删除帐户
要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户
LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。 

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。 

用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。 

然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。

如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分。 

![应用网站授予的 Azure Active Directory 权限](./media/luis-how-to-account-settings/tenant-permissions.png)

如果租户管理员只希望某些用户使用 LUIS，请参阅此[标识博客](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多个协作者电子邮件的用户帐户
如果将协作者添加到 LUIS 应用，则要指定协作者将 LUIS 用作协作者所需的确切电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单名用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用协作者列表中指定的电子邮件地址登录。 


## <a name="next-steps"></a>后续步骤

深入了解[创作密钥](luis-concept-keys.md#authoring-key)。 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
