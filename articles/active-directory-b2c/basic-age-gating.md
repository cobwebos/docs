---
title: 使用 Azure Active Directory B2C 中的年龄门控 | Microsoft Docs
description: 学习如何辨别使用应用程序的未成年人。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056511"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>在 Azure AD B2C 中使用年龄门控

>[!IMPORTANT]
>此功能在个人预览版中提供。  当此功能变得可用时，请参阅我们的[服务博客](https://blogs.msdn.microsoft.com/azureadb2c/)了解详细信息，或联系 AADB2CPreview@microsoft.com。  在我们正式发布前，请勿将此功能用于生产目录，使用这些新功能可能会导致数据丢失，并可能会出现意外的行为更改。  
>

##<a name="age-gating"></a>年龄门控
使用年龄限制可通过 Azure AD B2C 辨别应用程序中的未成年人。  可以选择阻止用户登录到应用程序，或者允许他们带着标识用户的年龄组及其家长同意状态的附加声明返回到应用程序。  

>[!NOTE]
>可在名为 `consentProvidedForMinor` 的用户属性中跟踪家长同意状态。  可以通过图形 API 更新此属性，它将在更新 `legalAgeGroupClassification` 时使用此字段。
>

##<a name="setting-up-your-directory-for-age-gating"></a>为年龄门控设置目录
若要在用户流中使用年龄门控，需要将目录配置为具有附加属性。 此操作可以通过菜单中的 `Properties`（仅在使用个人预览版时才可用）完成。  
1. 在 Azure AD B2C 扩展的左侧菜单中，单击租户的“属性”。
2. 在“年龄门控”部分下，单击“配置”按钮。
3. 等待操作完成，系统将为年龄限制设置目录。

##<a name="enabling-age-gating-in-your-user-flow"></a>在用户流中启用“年龄限制”
目录设置为使用年龄门控后，便可以在预览版用户流中使用此功能了。  此功能需要的更改会使其与现有类型的用户流不兼容。  通过执行以下步骤启用“年龄门控”：
1. 创建预览版用户流。
2. 创建后，请转到菜单中的“属性”。
3. 在“年龄门控”部分中，按切换开关以启用该功能。
4. 然后可以选择要如何管理标识为未成年人的用户。

##<a name="what-does-enabling-age-gating-do"></a>启用“年龄门控”的作用是什么？
在用户流中启用“年龄门控”后，用户体验会发生变化。  注册时，现在将要求用户提供其出生日期和居住地所在国家/地区以及为用户流配置的用户属性。  登录时，先前未输入出生日期和居住地所在国家/地区的用户将被要求在下次登录时提供此信息。  Azure AD B2C 将根据这两个值确定该用户是否是未成年人并更新 `ageGroup` 字段，其值可以为 `null`、`Undefined`、`Minor`、`Adult` 和 `NotAdult`。  然后 `ageGroup` 和 `consentProvidedForMinor` 字段将用于计算 `legalAgeGroupClassification`。 

##<a name="age-gating-options"></a>年龄限制选项
可以选择让 Azure AD B2C 阻止未征得家长同意的未成年人，或者允许他们并让应用程序决定如何处理他们。  

###<a name="allowing-minors-without-parental-consent"></a>允许未征得家长同意的未成年人
对于允许注册和/或登录的用户流，可以选择允许未经同意的未成年人进入应用程序。  对于未征得家长同意的未成年人，允许他们照常登录或注册，并且 Azure AD B2C 会颁发一个带有 `legalAgeGroupClassification` 声明的 ID 令牌。  通过使用此声明，可以选择这些用户将拥有的体验，例如完成征得家长同意（并更新 `consentProvidedForMinor` 字段）的体验。

###<a name="blocking-minors-without-parental-consent"></a>阻止未征得家长同意的未成年人
对于允许注册和/或登录的用户流，可以选择阻止未经同意的未成年人进入应用程序。  在 Azure AD B2C 中有两个用于处理被阻止用户的选项：
* 将 JSON 发送回应用程序 - 此选项会将响应发送回阻止了未成年人的应用程序。
* 显示错误页 - 向用户显示一个页面，通知他们不能访问该应用程序