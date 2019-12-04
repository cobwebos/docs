---
title: 了解 Azure AD 架构和自定义表达式
description: 本主题介绍了 Azure AD 架构，即预配代理流和自定义表达式的属性。
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
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794492"
---
# <a name="understanding-the-azure-ad-schema"></a>了解 Azure AD 架构
Azure AD 中的对象（如任意目录）是一个编程的高级数据构造，它表示用户、组和联系人等。  在 Azure AD 中创建新用户或联系人时，将创建该对象的新实例。  这些实例可根据其属性进行区分。

在 Azure AD 中，属性是负责将对象实例的信息存储在 Azure AD 中的元素。  

Azure AD 架构定义了一些规则，这些规则可用于某个条目中的属性、这些属性可能具有的值种类，以及用户如何与这些值交互。 

Azure AD 具有两种类型的属性。  属性包括：
- **内置属性**– Azure AD 架构预定义的属性。  这些属性提供不同的用法，并且可能或不可访问。
- **目录扩展**–提供的属性，以便您可以自定义 Azure AD 以供自己使用。  例如，如果已将本地 Active Directory 与某个属性进行了扩展，并且想要流式传输该属性，则可以使用提供的其中一个自定义属性。 

## <a name="attributes-and-expressions"></a>特性和表达式
在将某个对象（例如用户）设置为 Azure AD 时，将创建该用户对象的新实例。  此创建包括该对象（也称为属性）的属性。  最初，新创建的对象的属性将设置为由同步规则确定的值。  然后，通过云预配代理将这些属性保持为最新。

![](media/concept-attributes/attribute1.png)

例如，如果某个用户是营销部门的一部分，则在设置该用户的 Azure AD 部门属性后，将会创建这些属性，然后将值设置为 "营销"。  但在六个月后，它们将更改为 Sales。  其本地 AD 部门属性更改为 Sales。  此更改将同步到 Azure AD 并在其 Azure AD 用户对象上进行反射。

属性同步可以是直接的，其中 Azure AD 中的值会直接设置为本地属性的值。  或者，可能有处理此同步的编程表达式。  在需要进行某些逻辑或确定以填充值的情况下，需要编程表达式。

例如，如果我有 mail 属性（"john.smith@contoso.com"），并且我需要去除 "@contoso.com" 部分，并只对值 "john smith" 进行处理，我将使用如下所示的内容：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**示例输入/输出：** <br>

* **输入** (mail)：“john.smith@contoso.com”
* **输出**： "john smith"

有关其他信息，请参阅编写自定义表达式和语法，请参阅[在 Azure Active Directory 中编写属性映射的表达式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)。

下面列出了常见的属性，以及如何将它们同步到 Azure AD。


|本地 Active Directory|映射类型|Azure AD|
|-----|-----|-----|
|cn|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|表达式|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="viewing-the-schema"></a>查看架构
若要查看并验证架构，请执行以下步骤：

1.  导航到[图形资源管理器](https://developer.microsoft.com/graph/graph-explorer)。
2.  用全局管理员帐户登录
3.  在左侧单击 "**修改权限**"，并确保 "**所有**" 都同意。
4.  运行以下查询： https://graph.microsoft.com/beta/serviceprincipals/ 。  此查询将返回服务主体的列表。
5.  找到 "appDisplayName"： "Active Directory Azure Active Directory 预配"，并记下 "id：" 值。
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
6. 将 {Service Principal id} 替换为你的值并运行以下查询： `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. 找到 "id"： "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" 部分，并记下 "id："。
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
8. 现在，请运行以下查询： `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    示例： https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 将 {Service Principal Id} 和 {AD2ADD 预配 Id} 替换为你的值。

9. 此查询将返回架构。
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
