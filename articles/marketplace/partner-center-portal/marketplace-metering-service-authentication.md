---
title: 市场计量服务身份验证策略 | Azure 市场
description: Azure 市场中支持的计量服务身份验证策略。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0f8078c52945b52a27144c1f73ea4a136bf536d8
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963149"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>市场计量服务身份验证策略

市场计量服务支持两种身份验证策略：

* [Azure AD 安全令牌](../../active-directory/develop/access-tokens.md)
* [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 

本文将介绍何时以及如何使用不同的身份验证策略并使用市场计量服务安全提交自定义计量器。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全令牌

适用的产品/服务类型为事务 SaaS 和具有托管应用程序计划类型的 Azure 应用程序。  

使用预定义的固定 Azure AD 应用程序 ID 提交自定义计量器进行身份验证。

对于 SaaS 服务，这是唯一可用的选项。 如[注册 saas 应用程序](./pc-saas-registration.md)中所述，发布任何 saas 产品/服务是必需的。

对于采用托管应用程序计划的 Azure 应用程序，在以下情况下应考虑使用此策略：

* 已有一种机制可用来与后端服务通信，并且你想要扩展此机制，以便从某个集中化服务发出自定义计量器。
* 存在复杂的自定义计量器逻辑。  在某个集中化位置而不是在托管应用程序资源中运行此逻辑。

注册应用程序后，可采用编程方式请求 Azure AD 安全令牌。 发布者预期使用此令牌，并发出请求来解析它。

有关这些令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](../../active-directory/develop/access-tokens.md)。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>基于 Azure AD 应用获取令牌

#### <a name="http-method"></a>HTTP 方法

**POST**

#### <a name="request-url"></a>*请求 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 参数*

|  **参数名称** |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 已注册的 Azure AD 应用程序的租户 ID。   |
| | | |

#### <a name="request-header"></a>请求标头

|  **标头名称**    |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 与请求关联的内容类型。 默认值是 `application/x-www-form-urlencoded`。  |
| | | |

#### <a name="request-body"></a>*请求正文*

|  **属性名称**  |  **必需**  |  **说明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 授权类型。 改用 `client_credentials` |
|  `Client_id`        |   True         | 与 Azure AD 应用关联的客户端/应用标识符。|
|  `client_secret`    |   True         | 与 Azure AD 应用相关联的机密。  |
|  `Resource`         |   True         | 为其请求令牌的目标资源。 改用 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` |
| | | |

#### <a name="response"></a>*响应*

|  **名称**    |  类型  |  **说明**          |
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

适用的产品/服务类型是采用托管应用程序计划类型的 Azure 应用程序。

使用此方法可使部署的资源标识能够进行身份验证，以发送自定义计量器使用情况事件。  可以在部署边界内嵌入用于发出使用情况的代码。

>[!Note]
>发布者应确保发出使用情况的资源被锁定，以免使用情况数据遭到篡改。

托管应用程序可以包含从虚拟机到 Azure Functions 在内的各种类型的资源。  有关如何使用不同服务的托管标识进行身份验证的详细信息，请参阅[如何使用 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)）。

例如，按照以下步骤使用 Windows VM 进行身份验证：

1. 确保使用以下方法之一来配置托管标识：
    * [Azure 门户 UI](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure 资源管理器模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity)）
    * [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. 使用系统标识获取市场计量服务应用程序 ID (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) 的访问令牌，通过 RDP 连接到 VM，打开 PowerShell 控制台，并运行以下命令

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 从当前资源组的“ManagedBy”属性中获取托管应用 ID

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. 市场计量服务要求报告有关 `resourceID` 的使用情况，如果是托管应用程序，则还要报告有关 `resourceUsageId` 的使用情况。

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. 使用[市场计量服务 API](./marketplace-metering-service-apis.md) 发出使用情况。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 应用程序产品/服务](./create-new-azure-apps-offer.md)
* [创建事务 SaaS 产品/服务](./offer-creation-checklist.md)