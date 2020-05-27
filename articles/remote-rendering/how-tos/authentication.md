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
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195303"
---
# <a name="configure-authentication"></a>配置身份验证

Azure 远程渲染使用与 [Azure 空间定位点 (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) 相同的身份验证机制。 客户端需要设置 AccountKey、AuthenticationToken 或 AccessToken 才能成功调用 REST API。 AccountKey 可以在 Azure 门户上远程渲染帐户的“密钥”选项卡中获得。 AuthenticationToken 是 Azure AD 令牌，可使用 [ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)获取。 AccessToken 是 MR 令牌，可从 Azure 混合现实安全令牌服务 (STS) 中获取。

## <a name="authentication-for-deployed-applications"></a>已部署应用程序的身份验证

 建议使用帐户密钥快速加入，但仅限在开发/原型设计期间使用。 强烈建议不要使用应用程序中的嵌入式帐户密钥将应用程序交付到生产环境，而应使用基于用户或基于服务的 Azure AD 身份验证方法。

 Azure AD 身份验证在 [Azure 空间定位点 (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) 服务的 [Azure AD 用户身份验证](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)部分中进行了说明。

## <a name="role-based-access-control"></a>基于角色的访问控制

我们创建了以下角色，用于控制向应用程序、服务或服务的 Azure AD 用户授予的访问权限的级别，供你根据需要向 Azure 远程渲染帐户分配：

* **远程渲染管理员**：为用户提供 Azure 远程渲染的转换、管理会话、渲染和诊断功能。
* **远程渲染客户端**：为用户提供 Azure 远程渲染的管理会话、渲染和诊断功能。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
