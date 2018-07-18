---
title: Azure Active Directory B2C 的 Twitter 配置 | Microsoft Docs
description: 在受 Azure Active Directory B2C 保护的应用程序中为使用 Twitter 帐户的客户提供注册和登录功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68b9e9edbacda08bc98b6b7af5d944d3df9edca1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709574"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>使用 Azure AD B2C 为使用 Twitter 帐户的客户提供注册和登录功能

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序
要将 Twitter 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Twitter 应用程序并向其提供合适的参数。 需要一个 Twitter 帐户来执行此操作。 如果没有账户，可在 [https://twitter.com/signup](https://twitter.com/signup) 处获取。

1. 转到 [Twitter 应用](https://apps.twitter.com/)并使用你的凭据登录。
2. 单击“创建新应用”。 
3. 在表单中，提供**名称**、**说明**和**网站**的值。
4. 对于“回调 URL”，输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 确保将 **{tenant}** 替换为租户名称（例如 contosob2c.onmicrosoft.com）。
5. 选中框以同意**开发人员协议**，并单击“创建 Twitter 应用程序”。
6. 创建应用后，在列表中选择它，然后选择“设置”选项卡。
7. 清除“启用回叫锁定”框，然后单击“更新设置”。
8. 选择“密钥和访问令牌”选项卡。
9. 复制“使用者密钥”和“使用者密码”的值。 将 Twitter 配置为租户中的标识提供者时需要这两个值。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>将 Twitter 配置为租户中的标识提供者
1. 以 Azure AD B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 
2. 若要切换到 Azure AD B2C 租户，请在门户右上角选择 Azure AD B2C 目录。
3. 单击“所有服务”，然后从“安全 + 标识”下的服务列表中选择“Azure AD B2C”。
4. 单击“标识提供者”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“Twitter”。
5. 单击“标识提供者类型”，选择“Twitter(预览版)”，并单击“确定”。
6. 单击“设置此标识提供者”，针对“客户端 ID”，输入 Twitter **使用者密钥**，针对“客户端密码”输入 Twitter **使用者密码**。
7. 单击“确定”，并单击“创建”以保存 Twitter 配置。

## <a name="next-steps"></a>后续步骤

创建或编辑[内置策略](active-directory-b2c-reference-policies.md)并将 Twitter 添加为标识提供者。