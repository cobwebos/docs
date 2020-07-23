---
title: Azure SignalR 服务中的托管标识
description: 了解托管标识在 Azure SignalR 服务中的工作原理，以及如何在无服务器方案中使用托管标识。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559410"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR 服务的托管标识

本文介绍如何为 Azure SignalR 服务创建托管标识，以及如何在无服务器方案中使用它。

> [!Important] 
> Azure SignalR 服务只能支持一个托管标识。 这意味着，可以添加系统分配的标识或用户分配的标识。 

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识

若要在 Azure 门户中设置托管标识，请首先创建一个 Azure SignalR 服务实例，然后启用该功能。

1. 与往常一样，在门户中创建 Azure SignalR 服务实例。 在门户中浏览到该网站。

2. 选择“标识”。

4. 在 "**系统分配**" 选项卡上，将 "**状态**" 切换到 **"打开"**。 选择“保存”。

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="在门户中添加系统分配的标识":::

## <a name="add-a-user-assigned-identity"></a>添加用户分配的标识

创建具有用户分配的标识的 Azure SignalR 服务实例需要创建标识，然后将其资源标识符添加到服务。

1. 根据[这些说明](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)创建用户分配的托管标识资源。

2. 与往常一样，在门户中创建 Azure SignalR 服务实例。 在门户中浏览到该网站。

3. 选择“标识”。

4. 在 "**用户分配**" 选项卡上，选择 "**添加**"。

5. 搜索之前创建的标识，然后选择它。 选择 **添加** 。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="在门户中添加用户分配的标识":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>在无服务器方案中使用托管标识

Azure SignalR 服务是一种完全托管的服务，因此不能使用托管标识手动获取令牌。 相反，Azure SignalR 服务使用设置的托管标识来获取访问令牌。 然后，服务 `Authorization` 在无服务器方案中将访问令牌设置为上游请求中的标头。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>在上游设置中启用托管标识身份验证

1. 添加系统分配的标识或用户分配的标识。

2. 配置上游设置并使用**对 microsoft.managedidentity**作为**身份验证**设置。 若要了解如何通过身份验证创建上游设置，请参阅[上游设置](concept-upstream.md)。

3. 在 "**资源**的托管标识身份验证设置" 中，可以指定目标资源。 资源将成为 `aud` 获取的访问令牌中的声明，可在上游终结点中用作验证的一部分。 资源可以是以下项之一：
    - 空
    - 应用程序（客户端）服务主体的 ID
    - 服务主体的应用程序 ID URI
    - [Azure 服务的资源 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 如果在服务中亲自验证访问令牌，则可以选择任何一个资源格式。 只需确保 "**身份**验证设置" 和 "验证" 中的**资源**值一致。 如果对数据平面使用基于角色的访问控制（RBAC），则必须使用服务提供商请求的资源。

### <a name="validate-access-tokens"></a>验证访问令牌

标头中的令牌 `Authorization` 为[Microsoft 标识平台访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens)。

若要验证访问令牌，应用还应验证受众和签名令牌。 这些需要根据 OpenID 发现文档中的值进行验证。 例如，请参阅[独立于租户的文档版本](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure Active Directory （Azure AD）中间件具有验证访问令牌的内置功能。 可以浏览我们的示例，查找所选语言的[示例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)。

我们提供了库和代码示例，用以演示如何轻松处理令牌验证。 还有多个开源合作伙伴库可用于 JSON Web 令牌（JWT）验证。 几乎每个平台和语言都至少有一个选项。 有关 Azure AD 身份验证库和代码示例的详细信息，请参阅[Microsoft 标识平台身份验证库](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)。

## <a name="next-steps"></a>后续步骤

- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
