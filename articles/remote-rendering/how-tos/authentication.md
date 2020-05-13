---
title: 身份验证
description: 说明身份验证的工作原理
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195303"
---
# <a name="configure-authentication"></a>配置身份验证

Azure 远程呈现使用与[Azure 空间定位点（ASA）](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)相同的身份验证机制。 客户端需要将*AccountKey*、 *AuthenticationToken*或*ACCESSTOKEN*设置为成功调用 REST api。 可以在 Azure 门户上的远程呈现帐户的 "密钥" 选项卡中获取*AccountKey* 。 *AuthenticationToken*是 Azure AD 令牌，可使用[ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)获取。 *AccessToken*是 MR 令牌，可从 Azure Mixed Reality 安全令牌服务（STS）中获取。

## <a name="authentication-for-deployed-applications"></a>已部署的应用程序的身份验证

 建议快速使用帐户密钥，但仅限开发/原型开发期间使用。 强烈建议不要使用嵌入的帐户密钥将应用程序交付到生产环境，而是使用基于用户或基于服务的 Azure AD 身份验证方法。

 Azure AD 身份验证在[Azure 空间锚（ASA）](https://docs.microsoft.com/azure/spatial-anchors/)服务的 " [Azure AD 用户身份验证](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)" 部分中进行了介绍。

## <a name="role-based-access-control"></a>基于角色的访问控制

为了帮助控制向你的服务中的应用程序、服务或 Azure AD 用户授予的访问级别，为你创建了以下角色以根据你的 Azure 远程呈现帐户进行分配：

* **远程呈现管理员**：为用户提供对 Azure 远程呈现的转换、管理会话、呈现和诊断功能。
* **远程呈现客户端**：为用户提供 Azure 远程呈现的管理会话、呈现和诊断功能。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [使用 Azure 前端 Api 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
