---
title: 适用于 Microsoft 商业应用商店中的 Azure 应用的合作伙伴中心提交 API
description: 了解在 Microsoft 合作伙伴中心的商业 marketplace 中使用适用于 Azure 应用的合作伙伴中心提交 API 的先决条件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2c37e51ad0c6618e20d9445fab7472b1a3a72ab9
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744895"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>合作伙伴中心提交的 API，可在合作伙伴中心加入 Azure 应用

使用*合作伙伴中心提交 API*以编程方式查询、创建提交并发布 Azure 产品/服务。  如果你的帐户管理多个产品/服务，并且你希望自动执行这些服务的提交过程并对其进行优化，则此 API 非常有用。

## <a name="api-prerequisites"></a>API 先决条件

你需要使用几种编程资产才能使用适用于 Azure 产品的合作伙伴中心 API： 

- Azure Active Directory 应用程序。
- Azure Active Directory （Azure AD）访问令牌。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步骤1：完成使用合作伙伴中心提交 API 的先决条件

在开始编写代码以调用合作伙伴中心提交 API 之前，请确保已完成以下先决条件。

- 你（或你的组织）必须具有 Azure AD 目录，并且你必须对该目录拥有[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限。 如果你已使用 Office 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。 否则，你可以[在合作伙伴中心创建新的 Azure AD，而](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)无额外费用。

- 必须[将 Azure AD 应用程序与合作伙伴中心帐户相关联](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)，并获取租户 id、客户端 id 和密钥。 获取 Azure AD 访问令牌（该令牌用于调用 Microsoft Store 提交 API）需要这些值。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何将 Azure AD 应用程序与合作伙伴中心帐户关联

若要使用 Microsoft Store 提交 API，你必须将 Azure AD 应用程序与你的合作伙伴中心帐户关联、检索应用程序的租户 ID 和客户端 ID，并生成一个密钥。 Azure AD 应用程序表示你要从中调用合作伙伴中心提交 API 的应用或服务。 你需要租户 ID、客户端 ID 和密钥以获取传递给 API 的 Azure AD 访问令牌。

>[!Note]
>只需执行此任务一次。 获取租户 ID、客户端 ID 和密钥后，当你需要创建新的 Azure AD 访问令牌时，可以随时重复使用它们。

1. 在合作伙伴中心，[将组织的合作伙伴中心帐户与组织的 Azure AD 目录相关联](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)。
1. 接下来，从合作伙伴中心的 "**帐户设置**" 部分的 "**用户**" 页中，添加表示将用于访问合作伙伴中心帐户提交的应用或服务的[Azure AD 应用程序](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account)。 请确保为此应用程序分配**管理员**角色。 如果 Azure AD 目录中尚不存在该应用程序，则可以[在合作伙伴中心创建新的 Azure AD 应用程序](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。
1. 返回到**用户**页面、单击 Azure AD 应用程序的名称以转到应用程序设置，然后记下**租户 ID** 和**客户端 ID** 值。
1. 单击 "**添加新项**"。 在接下来的屏幕上，记下“密钥”值。**** 在离开此页面后，你将无法再访问该信息。 有关详细信息，请参阅[管理 Azure AD 应用程序的密钥](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步骤 2：获取 Azure AD 访问令牌

在合作伙伴中心提交 API 中调用任何方法之前，必须先获取一个 Azure AD 访问令牌，并将其传递给 API 中每个方法的**Authorization**标头。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 令牌过期后，您可以刷新该令牌，以便以后在对 API 的调用中继续使用该令牌。

若要获取访问令牌，请按照[使用客户端凭据的服务到服务调用](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)中的说明`HTTP POST`将发送`https://login.microsoftonline.com/<tenant_id>/oauth2/token`到终结点。 下面是示例请求：

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

对于`POST URI`和*client_id*和*client_secret*参数中的*tenant_id*值，请为你在上一节中的合作伙伴中心检索的应用程序指定租户 id、客户端 id 和密钥。 对于 *resource* 参数，必须指定 `https://api.partner.microsoft.com`。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>步骤 3：使用 Microsoft Store 提交 API

拥有 Azure AD 访问令牌后，可以在合作伙伴中心提交 API 中调用方法。 若要创建或更新提交，通常按特定顺序在合作伙伴中心提交 API 中调用多个方法。 有关每个方案和每个方法的语法的信息，请参阅摄取 API swagger。

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>后续步骤

* 了解如何创建[AZURE VM 技术资产](create-azure-container-technical-assets.md)
* 了解如何创建[Azure 容器产品/服务](create-azure-container-offer.md)hoe
