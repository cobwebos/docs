---
title: 管理身份验证
titleSuffix: Azure Maps
description: 熟悉 Azure Maps 身份验证。 了解哪种方法最适合哪种情况。 了解如何使用门户查看身份验证设置。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3383971b9984e6f565feebc3bdfb968fb77cbe85
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311092"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，将创建一个客户端 ID 和密钥以支持 Azure Active Directory (Azure AD) 身份验证和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 当 [使用共享密钥身份验证调用 Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)时，建议使用主密钥作为订阅密钥。 你可以在某些情况下使用辅助密钥，例如滚动密钥更改。 有关详细信息，请参阅 [Azure Maps 中的身份验证](https://aka.ms/amauth)。

可以在 Azure 门户中查看身份验证详细信息。 在帐户的 " **设置** " 菜单上，选择 " **身份验证**"。

> [!div class="mx-imgBorder"]
> ![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>发现类别和方案

根据应用程序的需要，有特定的路径来保护应用程序。 Azure AD 定义类别以支持各种身份验证流。 请参阅 [应用程序类别](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories) ，了解应用程序适合的类别。

> [!NOTE]
> 即使使用共享密钥身份验证，了解类别和方案也有助于保护应用程序的安全。

## <a name="determine-authentication-and-authorization"></a>确定身份验证和授权

下表概述了 Azure Maps 中常见的身份验证和授权方案。 该表提供了每个方案所提供的保护类型的比较。

> [!IMPORTANT]
> Microsoft 建议实现 Azure Active Directory () Azure AD 针对生产应用程序使用基于角色的访问控制 (RBAC) 。

| 方案                                                                                    | 身份验证 | 授权 | 开发工作 | 运营工作量 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | 共享密钥     | 空值           | 中型             | 高               |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | 中             |
| [具有交互式单一登录的 Web 单页面应用程序](./how-to-secure-spa-users.md) | Azure AD       | 高          | 中             | 中             |
| [具有非交互式登录的 Web 单页面应用程序](./how-to-secure-spa-app.md)      | Azure AD       | 高          | 中             | 中             |
| [交互式单一登录的 Web 应用程序](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | 中等             |
| [IoT 设备/输入受限制设备](./how-to-secure-device-code.md)                     | Azure AD       | 高          | 中             | 中             |

表中的链接可将你带到每个方案的详细配置信息。

## <a name="view-role-definitions"></a>查看角色定义

若要查看可用于 Azure Maps 的 Azure 角色，请 **访问 (IAM) 的 "访问控制 **"。 选择 " **角色**"，然后搜索以 *Azure Maps*开头的角色。 这些 Azure Maps 角色是可以向其授予访问权限的角色。

> [!div class="mx-imgBorder"]
> ![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>查看角色分配

若要查看已被授予 Azure Maps RBAC 的用户和应用，请 **访问 (IAM) 的 "访问控制 **"。 在此处选择 " **角色分配**"，然后按 **Azure Maps**进行筛选。

> [!div class="mx-imgBorder"]
> ![查看已获 RBAC 的用户和应用](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

从 Azure AD 令牌终结点请求令牌。 在 Azure AD 请求中，使用以下详细信息：

| Azure 环境      | Azure AD 令牌终结点             | Azure 资源 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 公有云     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府版云 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

有关请求用户和服务主体 Azure AD 的访问令牌的详细信息，请参阅在[方案](./how-to-manage-authentication.md#determine-authentication-and-authorization)表中[Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)和查看特定方案。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure AD 和 Azure Maps WEB SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
