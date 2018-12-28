---
title: 在 Azure Active Directory B2C 中注册应用程序 | Microsoft Docs
description: 了解如何将应用程序注册到 Azure Active Directory B2C。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fcebada4ef10c3e0bb298e9308d66ecb37247832
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999250"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中注册应用程序

若要生成可接受使用者注册和登录的[应用程序](active-directory-b2c-apps.md)，首先需要将此应用程序注册到 Azure AD B2C 租户。 本文将帮助你在数分钟内在 Azure Active Directory (Azure AD) B2C 租户中注册一个应用程序。 完成后，应用程序处于已注册状态，可以在 Azure AD B2C 租户中使用了。

## <a name="prerequisites"></a>先决条件

遵循[创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)中的步骤获取自己的租户。

根据应用程序类型选择后续步骤：

- [注册 Web 应用程序](#register-a-web-application)
- [注册 Web API](#register-a-web-api)
- [注册移动或本机应用程序](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>注册 Web 应用程序

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
3. 选择“应用程序”，然后选择“添加”。
4. 输入应用程序的名称。 例如 *testapp1*。
5. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
6. 对于“回复 URL”，请输入 Azure AD B2C 要将应用请求的任何令牌返回到的终结点。 例如，可将其设置为在 `https://localhost:44316` 上本地侦听。 如果尚不知道端口号，可以先输入占位符值，以后再进行更改。
7. 单击“创建”。

### <a name="create-a-client-secret"></a>创建客户端机密

如果应用程序调用受 Azure AD B2C 保护的 Web API，则你需要创建应用程序机密。

1. 选择“密钥”，然后单击“生成密钥”。 
2. 选择“保存”以查看密钥。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。
3. 选择“API 访问权限”，单击“添加”，然后选择 Web API 和范围（权限）。

## <a name="register-a-web-api"></a>注册 Web API

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
3. 选择“应用程序”，然后选择“添加”。
4. 输入应用程序的名称。 例如 *testapp2*。
5. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
6. 对于“回复 URL”，请输入 Azure AD B2C 要将应用请求的任何令牌返回到的终结点。 例如，可将其设置为在 `https://localhost:44316` 上本地侦听。 如果尚不知道端口号，可以先输入占位符值，以后再进行更改。
7. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
8. 单击“创建”。
9. 选择“发布的范围”，根据需要添加更多范围。 默认已定义 `user_impersonation` 范围。 `user_impersonation` 范围使其他应用程序可以代表已登录用户访问此 API。 如果需要，可以删除 `user_impersonation` 范围。

## <a name="register-a-mobile-or-native-application"></a>注册移动或本机应用程序

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
3. 选择“应用程序”，然后选择“添加”。
4. 输入应用程序的名称。 例如 *testapp3*。
5. 对于“包括 Web 应用/Web API”，请选择“否”。
6. 对于“包括本机客户端”，请选择“是”。
7. 对于“重定向 URI”，请输入[使用自定义方案的重定向 URI](active-directory-b2c-apps.md)。 请务必选择正确的重定向 URI ，且不能包含下划线等特殊字符。
8. 单击“创建”。

### <a name="create-a-client-secret"></a>创建客户端机密

如果应用程序调用受 Azure AD B2C 保护的 Web API，则你需要创建应用程序机密。

1. 选择“密钥”，然后单击“生成密钥”。 
2. 选择“保存”以查看密钥。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。
3. 选择“API 访问权限”，单击“添加”，然后选择 Web API 和范围（权限）。

## <a name="next-steps"></a>后续步骤

详细了解授予对[请求访问令牌](active-directory-b2c-access-tokens.md)中的 API 的权限的应用程序如何使用访问令牌
