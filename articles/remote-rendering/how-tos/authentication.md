---
title: 身份验证
description: 解释身份验证的工作原理
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681631"
---
# <a name="configure-authentication"></a>配置身份验证

Azure 远程呈现使用与 Azure[空间锚点 （ASA）](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)相同的身份验证机制。 客户端需要设置*帐户密钥*、*身份验证令牌*或*AccessToken*才能成功调用 REST API。 *帐户密钥*可以在 Azure 门户上的远程呈现帐户的"密钥"选项卡中获取。 *身份验证令牌*是 Azure AD 令牌，可以使用[ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)获取。 *AccessToken*是一个 MR 令牌，可以从 Azure 混合现实安全令牌服务 （STS） 获取。

## <a name="authentication-for-deployed-applications"></a>已部署应用程序的身份验证

 建议使用帐户密钥快速载入，但仅在开发/原型设计期间使用。 强烈建议不要使用其中的嵌入式帐户密钥将应用程序运送到生产部门，而是使用基于用户或基于服务的 Azure AD 身份验证方法。

 Azure AD 身份验证在[Azure 空间锚点 （ASA）](https://docs.microsoft.com/azure/spatial-anchors/)服务的[Azure AD 用户身份验证](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)部分中介绍。

## <a name="role-based-access-control"></a>基于角色的访问控制

为了帮助控制授予服务的应用程序、服务或 Azure AD 用户的访问级别，已创建以下角色，以便您根据需要根据 Azure 远程呈现帐户进行分配：

* **远程呈现管理员**：为用户提供 Azure 远程呈现的转换、管理会话、呈现和诊断功能。
* **远程呈现客户端**：为用户提供 Azure 远程呈现的管理会话、呈现和诊断功能。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [电源外壳脚本示例](../samples/powershell-example-scripts.md)
