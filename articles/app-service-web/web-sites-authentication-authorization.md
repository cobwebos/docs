---
title: "在 Azure 应用中使用本地 Active Directory 进行身份验证 | Microsoft Docs"
description: "了解 Azure App Service 中的业务线应用在本地 Active Directory 上进行身份验证时可用的不同选项"
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: dde6b7e6-bf6a-4fa5-8390-3a18155d21bd
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f989ea86c76706af9413b2bb945956b1c6432e1d


---
# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Authenticate with on-premises Active Directory in your Azure app（在 Azure 应用中使用本地 Active Directory 进行身份验证）
本文说明如何在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中使用本地 Active Directory (AD) 进行身份验证。 Azure 应用托管在云中，但可以通过多种方法安全地对本地 AD 用户进行身份验证。 

## <a name="authenticate-through-azure-active-directory"></a>通过 Azure Active Directory 进行身份验证
Azure Active Directory 租户的目录可与本地 AD 同步。 此方法可让 AD 用户通过 Internet 访问应用，并使用其本地凭据进行身份验证。 此外，Azure App Service [为此方法提供周全的解决方案](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。 只需按几下鼠标，就能为 Azure 应用启用目录同步的租户的身份验证。 此方法具有以下优点：

* 应用中不需要任何身份验证代码。 让应用服务自动执行身份验证，腾出时间专注于如何在应用中提供功能。
* 使用 [Azure AD 图形 API](http://msdn.microsoft.com/library/azure/hh974476.aspx) 可从 Azure 应用访问目录数据。
* 将 SSO 提供给 [Azure Active Directory 支持的所有应用程序](/marketplace/active-directory/)，包括 Office 365、Dynamics CRM Online、Microsoft Intune 和数千个非 Microsoft 云应用程序。 
* Azure Active Directory 支持基于角色的访问控制。 可以使用 [Authorize(Roles="X")] 模式，并且只需对代码进行最少量的更改。

若要了解如何编写使用 Azure Active Directory 进行身份验证的业务线 Azure 应用，请参阅[使用 Azure Active Directory 身份验证创建业务线 Azure 应用](web-sites-dotnet-lob-application-azure-ad.md)。

## <a name="authenticate-through-an-on-premises-sts"></a>通过本地 STS 进行身份验证
如果已安装本地安全令牌服务 (STS)（例如 Active Directory 联合身份验证服务 (AD FS)），可以使用该服务联合 Azure 应用的身份验证。 如果公司政策禁止将 AD 数据存储在 Azure 中，这是最合适的方法。 但是，请注意以下事项：

* STS 拓扑必须在本地部署，这会产生成本和管理开销。
* 只有 AD FS 管理员可以配置[信赖方信任和声明规则](http://technet.microsoft.com/library/dd807108.aspx)，这可能会限制开发人员的选择。 另一方面，可以根据应用程序管理和自定义[声明](http://technet.microsoft.com/library/ee913571.aspx)。
* 需要提供单独的解决方案通过公司防火墙访问本地 AD 数据。

若要了解如何编写使用本地 STS 进行身份验证的业务线 Azure 应用，请参阅[使用 AD FS 身份验证创建业务线 Azure 应用](web-sites-dotnet-lob-application-adfs.md)。




<!--HONumber=Nov16_HO3-->


