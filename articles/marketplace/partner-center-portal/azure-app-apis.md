---
title: 商业应用商店中 Azure 应用的载入 API
description: 在 Microsoft 合作伙伴中心的商业市场中，Azure 应用的 API 先决条件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279753"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>用于在合作伙伴中心中载入 Azure 应用的 API

使用*合作伙伴中心提交 API*以编程方式查询、为 Azure 产品/服务创建提交并发布 Azure 产品/服务。  如果您的帐户管理许多产品/服务，并且希望自动和优化这些优惠的提交过程，则此 API 非常有用。

## <a name="api-prerequisites"></a>API 先决条件

要使用 Azure 产品的合作伙伴中心 API，您需要一些编程资源： 

- Azure 活动目录应用程序。
- Azure 活动目录 （Azure AD） 访问令牌。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步骤 1：完成使用合作伙伴中心提交 API 的先决条件

在开始编写代码以调用合作伙伴中心提交 API 之前，请确保您已完成以下先决条件。

- 您（或您的组织）必须具有 Azure AD 目录，并且必须具有该目录的[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限。 如果你已使用 Office 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。 否则，您可以在[合作伙伴中心中创建新的 Azure AD，](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)无需支付额外费用。

- 您必须[将 Azure AD 应用程序与合作伙伴中心帐户相关联，](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)并获取租户 ID、客户端 ID 和密钥。 获取 Azure AD 访问令牌（该令牌用于调用 Microsoft Store 提交 API）需要这些值。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何将 Azure AD 应用程序与合作伙伴中心帐户关联

若要使用 Microsoft 应用商店提交 API，必须将 Azure AD 应用程序与合作伙伴中心帐户关联，检索应用程序的租户 ID 和客户端 ID，并生成密钥。 Azure AD 应用程序表示要从中调用合作伙伴中心提交 API 的应用或服务。 你需要租户 ID、客户端 ID 和密钥以获取传递给 API 的 Azure AD 访问令牌。

>[!Note]
>您只需要执行此任务一次。 获取租户 ID、客户端 ID 和密钥后，当你需要创建新的 Azure AD 访问令牌时，可以随时重复使用它们。

1. 在合作伙伴中心中，[将组织的合作伙伴中心帐户与组织的 Azure AD 目录相关联](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)。
1. 接下来，从合作伙伴中心"**帐户设置"** 部分中的 **"用户"** 页面[中，添加 Azure AD 应用程序](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account)，该应用程序表示将用于访问合作伙伴中心帐户提交的应用或服务。 请确保为此应用程序分配**管理员**角色。 如果应用程序在 Azure AD 目录中尚不存在，则可以[在合作伙伴中心中创建新的 Azure AD 应用程序](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。
1. 返回到**用户**页面、单击 Azure AD 应用程序的名称以转到应用程序设置，然后记下**租户 ID** 和**客户端 ID** 值。
1. 单击 **"添加新键**"。 在接下来的屏幕上，记下“密钥”值。**** 在离开此页面后，你将无法再访问该信息。 有关详细信息，请参阅[管理 Azure AD 应用程序的密钥](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步骤 2：获取 Azure AD 访问令牌

在调用合作伙伴中心提交 API 中的任何方法之前，必须首先获取 Azure AD 访问令牌，该令牌将传递给 API 中每个方法**的授权**标头。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 令牌过期后，可以刷新令牌，以便在以后调用 API 时继续使用它。

要获取访问令牌，请按照[服务到服务调用中的说明使用客户端凭据](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)将 发送到`HTTP POST``https://login.microsoftonline.com/<tenant_id>/oauth2/token`终结点。 下面是示例请求：

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

对于 和*client_id* `POST URI`和*client_secret*参数中的*tenant_id*值，请在上一节中指定从合作伙伴中心检索的应用程序的租户 ID、客户端 ID 和密钥。 对于 *resource* 参数，必须指定 `https://api.partner.microsoft.com`。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>步骤 3：使用 Microsoft Store 提交 API

获得 Azure AD 访问令牌后，可以在合作伙伴中心提交 API 中调用方法。 要创建或更新提交，通常按特定顺序在合作伙伴中心提交 API 中调用多个方法。 有关每个方案以及每个方法的语法的信息，请参阅引入 API 挥霍。

https://apidocs.microsoft.com/services/partneringestion/
