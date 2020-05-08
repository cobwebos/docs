---
title: Marketplace 计量服务身份验证策略 |Azure Marketplace
description: Azure Marketplace 中支持的计量服务身份验证策略。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736196"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace 计量服务身份验证策略

Marketplace 计量服务支持两种身份验证策略：

* [Azure AD 安全令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

我们将介绍何时以及如何使用不同的身份验证策略，以使用 Marketplace 计量服务安全地提交自定义计量。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全令牌

适用的产品/服务类型为 SaaS 和具有托管应用程序计划类型的 Azure 应用程序。  

通过使用预定义的固定应用程序 ID 进行身份验证来提交自定义计量。

对于 SaaS 产品/服务，Azure AD 是唯一可用的选项。

对于使用托管应用程序计划的 Azure 应用程序，应考虑在以下情况下使用此策略：

* 你已经有一种机制可以与后端服务进行通信，并且你想要扩展此机制以便从中央服务发出自定义计量。
* 你有复杂的自定义计量逻辑。  在中心位置（而不是托管的应用程序资源）运行此逻辑。

注册应用程序后，可以以编程方式请求 Azure AD 安全令牌。 发行者应使用此令牌并发出请求以解决此问题。

有关这些令牌的详细信息，请参阅[Azure Active Directory 访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>基于 Azure AD 应用获取令牌

#### <a name="http-method"></a>HTTP 方法

**发布**

#### <a name="request-url"></a>*请求 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 参数*

|  **参数名称** |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 已注册 Azure AD 应用程序的租户 ID。   |
| | | |

#### <a name="request-header"></a>*请求标头*

|  **标头名称**    |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 与请求关联的内容类型。 默认值为 `application/x-www-form-urlencoded`。  |
| | | |

#### <a name="request-body"></a>*请求正文*

|  **属性名称**  |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 授权类型。 默认值为 `client_credentials`。 |
|  `Client_id`        |   True         | 与 Azure AD 应用关联的客户端/应用标识符。|
|  `client_secret`    |   True         | 与 Azure AD 应用关联的密码。  |
|  `Resource`         |   True         | 为其请求令牌的目标资源。 默认值为 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`。  |
| | | |

#### <a name="response"></a>*响应*

|  **名称**    |  **类型**  |  **说明**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | 请求成功。  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

示例响应令牌：

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>使用 Azure 托管标识令牌

适用的产品/服务类型为 Azure 应用程序和托管应用程序计划类型。

使用此方法可允许已部署的资源标识进行身份验证，以发送自定义计量器使用事件。  你可以嵌入在你的部署边界内发出使用的代码。

>[!Note]
>发布者应确保发出使用情况的资源被锁定，因此不会被篡改。

托管应用程序可以包含不同类型的资源，从虚拟机到 Azure Functions。  有关如何使用不同服务的托管标识进行身份验证的详细信息，请参阅[如何使用 Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)。

例如，按照以下步骤使用 Windows VM 进行身份验证，

1. 请确保使用以下方法之一配置托管标识：
    * [Azure 门户 UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Azure 资源管理器模板](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. 使用系统标识、RDP 连接到 VM，打开 PowerShell`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`控制台，并运行以下命令，获取 Marketplace 计量服务应用程序 ID （）的访问令牌

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 从当前资源组 "ManagedBy" 属性获取托管应用 ID

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace 计量服务需要报告的`resourceID`使用情况以及`resourceUsageId`托管应用程序。

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. 使用[Marketplace 计量服务 API](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847)发出使用情况。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 应用程序产品/服务](./create-new-azure-apps-offer.md)