---
title: 为 Azure Spring Cloud 应用程序启用系统分配的托管标识
description: 如何为应用程序启用系统分配的托管标识。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1b9d7326ec13176fbe65ba430a8a33bb93a48f74
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091447"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识
Azure 资源的托管标识提供了自动管理的标识，可在 Azure Active Directory azure 应用程序之类的 Azure 资源。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。

本文介绍如何使用 Azure 门户和 CLI （版本0.2.4 中提供）为 Azure 春季云应用启用和禁用系统分配的托管标识。

## <a name="prerequisites"></a>先决条件
如果不熟悉 Azure 资源的托管标识，请参阅[概述部分](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
需要部署的 Azure 春季云实例。 按照[使用 Azure CLI 进行部署的快速入门](spring-cloud-quickstart-launch-app-cli.md)。

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识
使用系统分配的标识创建应用时，需要在应用程序上设置其他属性。

### <a name="using-azure-portal"></a>使用 Azure 门户
若要在[Azure 门户](https://portal.azure.com/)中设置托管标识，请首先创建应用，然后启用该功能。

1. 按常规在门户中创建应用。 在门户中导航到该应用。
2. 向下滚动到左侧导航窗格中的 "**设置**" 组。
3. 选择“标识”。
4. 在“系统分配的”选项卡中，将“状态”切换为“启用”  。 单击“保存” 。

 ![门户中的托管标识](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
可以在应用程序创建过程中或在现有应用程序上启用系统分配的托管标识。

**在创建应用期间启用系统分配的托管标识**

下面的示例使用系统分配的托管标识创建一个名为*app_name*的应用，如参数所请求 `--assign-identity` 。

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**在现有应用程序上启用系统分配的托管标识**使用 `az spring-cloud app identity assign` 命令在现有应用程序上启用系统分配的标识。

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>获取 Azure 资源的令牌
应用可以使用其托管标识来获取令牌，以访问受 Azure Active Directory 保护的其他资源，如 Azure Key Vault。 这些令牌代表访问资源的应用程序，而不是应用程序的任何特定用户。

你可能需要[配置目标资源以允许从你的应用程序访问](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal)。 例如，如果请求令牌来访问 Key Vault，请确保已添加包含应用程序标识的访问策略。 否则，对 Key Vault 的调用将被拒绝，即使其中包含令牌。 若要详细了解支持 Azure Active Directory 令牌的资源，请参阅[支持 Azure AD 身份验证的 Azure 服务](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)。

Azure 春季云与 Azure 虚拟机共享用于令牌的相同终结点。 建议使用 Java SDK 或弹簧 boot 初学者来获取令牌。  有关处理令牌过期和 HTTP 错误等重要主题，请参阅[如何将 VM 令牌](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)用于各种代码和脚本示例和指导。

建议：使用 Java SDK 或弹簧 boot 初学者初学者获取令牌。  请参阅[后续步骤](#next-steps)中的示例。

## <a name="disable-system-assigned-identity-from-an-app"></a>禁用应用程序的系统分配的标识
删除系统分配的标识还会将其从 Azure AD 中删除。 删除应用资源会自动从 Azure AD 删除系统分配的标识。

### <a name="using-azure-portal"></a>使用 Azure 门户
若要从不再需要的应用程序中删除系统分配的托管标识：

1. 使用与包含 Azure 春季云实例的 Azure 订阅关联的帐户登录到[Azure 门户](https://portal.azure.com/)。
1. 导航到所需的虚拟机，然后选择“标识”。****
1. 在 "**系统已分配** / **状态**" 下，选择 "**关闭**"，然后单击 "**保存**"：

 ![门户中的托管标识](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
若要从不再需要的应用程序中删除系统分配的托管标识，请使用以下命令：
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>后续步骤
* [如何将托管标识用于 Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
* [使用春季 boot 入门中的托管标识访问 Azure Key Vault](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)

