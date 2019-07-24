---
title: Azure Active Directory (Azure AD) 身份验证
titleSuffix: Azure Cognitive Services
description: 沉浸式读者 SDK 参考
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 8af56d0d7c0f320e607421f12effdb9b37d5f78d
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444611"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>将 Azure Active Directory (Azure AD) 身份验证用于沉浸式读者服务

在以下部分中, 你将使用 Azure Cloud Shell 环境或 Azure CLI 使用自定义子域创建新的沉浸式读者资源, 然后在 Azure 租户中配置 Azure AD。 完成初始配置后, 你将调用 Azure AD 以获取访问令牌, 这与使用沉浸式读者 SDK 时的操作方式类似。 如果遇到问题, 每个部分都提供了每个 Azure CLI 命令的可用选项的链接。

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>使用自定义子域创建沉浸式读者资源

1. 首先打开[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 然后[选择订阅](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. 接下来, 使用自定义子域[创建沉浸式读者资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 

   >[!NOTE]
   > 使用 launchAsync 函数启动读取器时, 将在沉浸式读取器 SDK 中使用子域名称。

   -SkuName 可以是 F0 (免费层) 或 S0 (标准层, 也可以在公共预览期间免费)。 S0 层的调用速率限制更高, 对调用次数没有每月配额。

   -Location 可以是以下任一项: `eastus`、 `westus`、 `australiaeast`、 `centralindia`、 `japaneast` `northeurope`、、`westeurope`
   
   -CustomSubdomainName 需要全局唯一, 并且不能包含特殊字符, 如: "."、"!"、","。


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   如果成功, 则资源**终结点**应显示资源独有的子域名称。

   这里, 我们将新创建的资源对象捕获到 **$resource**变量中, 因为稍后在授予对此资源的访问权限时将使用该对象。


   >[!NOTE]
   > 如果在 Azure 门户中创建资源, 则资源的 "名称" 将用作自定义子域。 可以通过转到 "资源概述" 页, 并在此处列出的终结点中查找子域, 在门户中检查子域名, 例如`https://[SUBDOMAIN].cognitiveservices.azure.com/`。 如果需要获取子域以便与 SDK 集成, 还可以在以后查看此内容。

   如果资源是在门户中创建的, 则还可以立即[获取现有资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0)。

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```   

## <a name="assign-a-role-to-a-service-principal"></a>向服务主体分配角色

现在, 你已具有与资源关联的自定义子域, 你需要将角色分配给服务主体。

1. 首先, 让我们[创建一个 Azure AD 应用程序](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   >[!NOTE]
   > 获取身份验证令牌时, 将使用密码, 也称为 "客户端密码"。

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   这里, 我们要将新创建的 Azure AD 应用对象捕获到 **$aadApp**变量中, 以便在下一步中使用。   

2. 接下来, 需要为 Azure AD 应用程序[创建服务主体](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   这里, 我们要将新创建的服务主体对象捕获到 **$principal**变量中, 以便在下一步中使用。


3. 最后一步是[将 "认知服务用户" 角色分配](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)给服务主体 (作用域为资源)。 通过分配角色, 你将授予服务主体对此资源的访问权限。 可以向同一服务主体授予对订阅中多个资源的访问权限。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > 需要为创建的每个沉浸式读者资源执行此步骤。 例如, 如果为不同的全局区域创建多个资源, 则需要对每个区域资源执行此步骤, 以便 Azure AD 的身份验证适用于所有这些区域资源。 或者, 如果在以后的某个时间点创建新资源, 则还需要为该新资源执行此角色分配步骤。


## <a name="obtain-an-azure-ad-token"></a>获取 Azure AD 令牌

在此示例中, 将使用你的密码对服务主体进行身份验证, 以获取 Azure AD 令牌。

1. 获取**TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. 获取令牌:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > 沉浸式读取器 SDK 使用令牌的 AccessToken 属性, 例如 $token。AccessToken. 有关详细信息, 请参阅 SDK[参考](reference.md)和代码[示例](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples)。

或者, 可以使用证书对服务主体进行身份验证。 除了服务主体以外, 还支持通过其他 Azure AD 应用程序委派权限的用户主体。 在这种情况下, 在获取令牌时, 系统将提示用户提供双因素身份验证, 而不是密码或证书。

## <a name="next-steps"></a>后续步骤

* 查看[教程](./tutorial.md)，了解使用沉浸式阅读器 SDK 还可以做什么
* 探索[沉浸式阅读器 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和[沉浸式阅读器 SDK 参考](./reference.md)