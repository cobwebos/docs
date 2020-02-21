---
title: 了解 Azure AD 架构和自定义表达式
description: 本文介绍了 Azure AD 架构、预配代理流的属性和自定义表达式。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d621646aaa5c8c44a20cf327cd10fa31990b0
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484530"
---
# <a name="understand-the-azure-ad-schema"></a>了解 Azure AD 架构
Azure Active Directory （Azure AD）中的对象（如任意目录）是一个编程的高级数据构造，它表示用户、组和联系人等。 在 Azure AD 中创建新用户或联系人时，将创建该对象的新实例。 这些实例可根据其属性进行区分。

Azure AD 中的属性是负责在 Azure AD 中存储对象的实例信息的元素。

Azure AD 架构定义了一些规则，这些规则可用于某个条目中的属性、这些属性可能具有的值种类，以及用户如何与这些值交互。 

Azure AD 具有两种类型的属性：
- **内置属性**：由 Azure AD 架构预定义的属性。 这些属性提供了不同的用法，并且可能或不可访问。
- **目录扩展**：提供的属性，可用于自定义 Azure AD 以供自己使用。 例如，如果已使用特定属性扩展了本地 Active Directory 并且想要流式传输该属性，则可以使用提供的自定义属性之一。 

## <a name="attributes-and-expressions"></a>特性和表达式
如果将某个对象（例如用户）设置为 Azure AD，则将创建该用户对象的新实例。 此创建包括该对象（也称为属性）的属性。 最初，新创建的对象的属性设置为由同步规则确定的值。 然后，通过云预配代理将这些属性保持为最新。

![对象预配](media/concept-attributes/attribute1.png)

例如，用户可能是市场营销部门的成员。 它们的 Azure AD 部门属性是在预配时开始创建的，并且值设置为 "营销"。 六个月后，如果它们更改为 Sales，则他们的本地 Active Directory 部门属性将更改为 Sales。 此更改将同步到 Azure AD 并在其 Azure AD 用户对象中反映出来。

属性同步可能是直接的，其中 Azure AD 中的值会直接设置为本地属性的值。 或者，编程表达式可能会处理同步。 在必须进行一些逻辑或确定来填充值的情况下，需要编程表达式。

例如，如果您具有 mail 属性 "john.smith@contoso.com"，并且需要去除 "@contoso.com" 部分并仅流式传输值 "john smith"，则可以使用如下所示的内容：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**示例输入/输出：** <br>

* **输入** (mail)：“john.smith@contoso.com”
* **输出**： "john smith"

有关如何编写自定义表达式和语法的详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)。

下表列出了常见的属性以及它们如何同步到 Azure AD。


|本地 Active Directory|映射类型|Azure AD|
|-----|-----|-----|
|cn|直接|commonName
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|表达式|givenName|
|objectGUID|直接|sourceAnchorBinary|  
|userprincipalName|直接|userPrincipalName|
|ProxyAdress|直接|ProxyAddress|

## <a name="view-the-schema"></a>查看架构
> [!WARNING]
> 云预配配置将创建服务主体。 服务主体在 Azure 门户中可见。 不应使用 Azure 门户中的服务主体体验来修改属性映射。  不支持此设置。

若要查看并验证架构，请执行以下步骤。

1.  请参阅[图形资源管理器](https://developer.microsoft.com/graph/graph-explorer)。
1.  用全局管理员帐户登录。
1.  在左侧，选择 "**修改权限**"，并确保 "**所有**" 都*同意*。
1.  运行查询 https://graph.microsoft.com/beta/serviceprincipals/?$filter = startswith （Displayname，"Active"）。 此查询返回已筛选的服务主体列表。
1.  找到 `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` 并记下 `"id"`的值。
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. 将 `{Service Principal id}` 替换为你的值，然后运行查询 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`。
1. 找到 `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` 并记下 `"id"`的值。
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. 现在运行查询 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`。
 
    示例： https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   将 `{Service Principal Id}` 和 `{AD2ADD Provisioning Id}` 替换为你的值。

1. 此查询将返回架构。

   ![返回的架构](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>后续步骤

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
