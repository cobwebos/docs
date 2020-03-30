---
title: 了解 Azure AD 架构和自定义表达式
description: 本文介绍 Azure AD 架构、预配代理流的属性和自定义表达式。
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
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299323"
---
# <a name="understand-the-azure-ad-schema"></a>了解 Azure AD 架构
与任何目录一样，Azure 活动目录 （Azure AD） 中的对象是一个编程性的高级数据构造，表示用户、组和联系人等内容。 在 Azure AD 中创建新用户或联系人时，将创建该对象的新实例。 这些实例可以根据其属性进行区分。

Azure AD 中的属性是负责存储有关 Azure AD 中对象实例的信息的元素。

Azure AD 架构定义可在条目中使用属性的规则、这些属性可能具有的值类型以及用户如何与这些值交互。 

Azure AD 有两种类型的属性：
- **内置属性**：由 Azure AD 架构预定义的属性。 这些属性提供不同的用途，并且可能访问也可能无法访问。
- **目录扩展**：提供的属性，以便您可以自定义 Azure AD 供自己使用。 例如，如果您已使用特定属性扩展本地活动目录，并希望流该属性，则可以使用提供的自定义属性之一。 

## <a name="attributes-and-expressions"></a>属性和表达式
当将用户等对象预配到 Azure AD 时，将创建用户对象的新实例。 此创建包括该对象的属性，该属性也称为属性。 最初，新创建的对象将其属性设置为由同步规则确定的值。 然后，这些属性通过云预配代理保持最新。

![对象预配](media/concept-attributes/attribute1.png)

例如，用户可能是市场营销部门的一部分。 其 Azure AD 部门属性最初在预配时创建，该值设置为市场营销。 六个月后，如果他们更改为"销售"，则其本地活动目录部门属性将更改为"销售"。 此更改同步到 Azure AD，并反映在其 Azure AD 用户对象中。

属性同步可能是直接的，其中 Azure AD 中的值直接设置为本地属性的值。 或者，编程表达式可能处理同步。 在必须确定某些逻辑或确定以填充值的情况下，需要编程表达式。

例如，如果您有邮件属性""，john.smith@contoso.com并且需要剥离""@contoso.com部分，并且只流值"john.smith"，则可以使用类似这样的内容：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**示例输入/输出：** <br>

* **输入** (mail)：“john.smith@contoso.com”
* **输出**：" 约翰.史密斯"

有关如何编写自定义表达式和语法的详细信息，请参阅[在 Azure 活动目录中编写属性映射的表达式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)。

下表列出了常见属性及其如何同步到 Azure AD。


|本地 Active Directory|映射类型|Azure AD|
|-----|-----|-----|
|cn|直接|通用名称
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|表达式|givenName|
|objectGUID|直接|源锚二进制|  
|userprincipalName|直接|userPrincipalName|
|代理阿莱斯|直接|ProxyAddress|

## <a name="view-the-schema"></a>查看架构
> [!WARNING]
> 云预配配置创建服务主体。 服务主体在 Azure 门户中可见。 不应使用 Azure 门户中的服务主体体验修改属性映射。  不支持此设置。

要查看架构并验证它，请按照以下步骤操作。

1.  转到[图形资源管理器](https://developer.microsoft.com/graph/graph-explorer)。
1.  使用全局管理员帐户登录。
1.  在左侧，选择**修改权限**并确保**目录.ReadWrite.所有内容***都已同意*。
1.  运行查询`https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`。 此查询返回经过筛选的服务主体列表。
1.  查找`"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`并记下 的值`"id"`。
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
1. 替换为`{Service Principal id}`值，然后运行查询`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`。
1. 查找`"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`并记下 的值`"id"`。
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
1. 现在运行查询`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`。
 
    示例： https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   替换`{Service Principal Id}`和`{AD2ADD Provisioning Id}`用您的值。

1. 此查询返回架构。

   ![返回的架构](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>后续步骤

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
