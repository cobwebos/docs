---
title: Azure SignalR 服务中的托管标识
description: 了解托管标识在 Azure SignalR 服务中的工作原理，以及如何在无服务器方案中使用托管标识。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: cc7082744bc43baad2e26d09a83907540cf6a1df
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094069"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR 服务的托管标识

本文介绍了如何为 Azure SignalR 服务创建托管标识，以及如何在无服务器方案中使用它。

> [!Important] 
> Azure SignalR 服务只能支持一个托管标识。 这意味着，你只能添加系统分配的标识或用户分配的标识。 

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识

若要在 Azure 门户中设置托管标识，需要首先创建一个 Azure SignalR 服务实例，然后启用该功能。

1. 像往常一样在门户中创建 Azure SignalR 服务实例。 在门户中浏览到它。

2. 选择“标识”。

4. 在“系统分配”选项卡中，将“状态”切换为“启用”  。 选择“保存” 。

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="在门户中添加系统分配的标识":::

## <a name="add-a-user-assigned-identity"></a>添加用户分配的标识

若要使用用户分配的标识创建 Azure SignalR 服务实例，需要先创建该标识，然后将其资源标识符添加到你的服务。

1. 根据[这些说明](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)创建用户分配的托管标识资源。

2. 像往常一样在门户中创建 Azure SignalR 服务实例。 在门户中浏览到它。

3. 选择“标识”。

4. 在“用户分配”选项卡上，选择“添加”。

5. 搜索之前创建的标识并选择它。 选择“添加”  。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="在门户中添加系统分配的标识":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>在无服务器方案中使用托管标识

Azure SignalR 服务是一种完全托管的服务，因此你不能使用托管标识来手动获取令牌， 而只能让 Azure SignalR 服务使用你设置的托管标识来获取访问令牌。 然后，该服务将访问令牌设置为无服务器方案的上游请求中的 `Authorization` 标头。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>在上游设置中启用托管标识身份验证

1. 添加系统分配的标识或用户分配的标识。

2. 配置上游设置并使用 **ManagedIdentity** 作为“身份验证”设置。 若要了解如何创建包含身份验证的上游设置，请参阅[上游设置](concept-upstream.md)。

3. 在托管标识身份验证设置中，对于“资源”，你可以指定目标资源。 资源将成为获取的访问令牌中的 `aud` 声明，可在上游终结点中用作验证的一部分。 资源可以是下列值之一：
    - 空
    - 服务主体的应用程序（客户端）ID
    - 服务主体的应用程序 ID URI
    - [Azure 服务的资源 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 如果你在服务中亲自验证访问令牌，则可选择任何一种资源格式。 只需要确保“身份验证”设置中的“资源”值与验证一致即可。 如果对数据平面使用 azure (azure RBAC) 的基于角色的访问控制，则必须使用服务提供商请求的资源。

### <a name="validate-access-tokens"></a>验证访问令牌

`Authorization` 标头中的令牌是 [Microsoft 标识平台访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens)。

若要验证访问令牌，你的应用还应当验证受众和签名令牌。 这些需要根据 OpenID 发现文档中的值进行验证。 有关示例，请参阅[文档的独立于租户的版本](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure Active Directory (Azure AD) 中间件具有用于验证访问令牌的内置功能。 你可以浏览我们的[示例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)来查找所选语言的示例。

我们提供了库和代码示例，用以演示如何轻松处理令牌验证。 还有多个可用于 JSON Web 令牌 (JWT) 验证的开源合作伙伴库。 几乎针对每种平台和语言都提供了至少一个选项。 有关 Azure AD 身份验证库和代码示例的详细信息，请参阅 [Microsoft 标识平台身份验证库](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)。

## <a name="next-steps"></a>后续步骤

- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
