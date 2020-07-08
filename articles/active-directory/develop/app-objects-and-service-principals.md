---
title: 应用 & Azure AD 中的服务主体 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Azure Active Directory 中应用程序对象与服务主体对象之间的关系。
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 453efd7735c6843ccdaf8dfd86b18d0b2ef8b06d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604618"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory 中的应用程序对象和服务主体对象

本文介绍了 Azure Active Directory 中的应用程序注册、应用程序对象和服务主体：它们是什么、它们的使用方式以及它们彼此之间的关系。 同时还会显示一个多租户示例方案，以说明应用程序的应用程序对象和对应的服务主体对象之间的关系。

## <a name="application-registration"></a>应用程序注册
为了将标识和访问管理功能委托给 Azure AD，应用程序必须向 Azure AD[租户](developer-glossary.md#tenant)注册。 向 Azure AD 注册应用程序时，将为应用程序创建标识配置，使其能够与 Azure AD 集成。 当你在[Azure 门户][AZURE-Portal]中注册应用时，你可以选择它是单租户（仅可在租户中访问），也可以选择多租户（可在其他租户中访问），还可以选择设置重定向 URI （将访问令牌发送到）。

完成应用注册后，你将拥有一个全局唯一的应用（应用程序对象）实例，该实例驻留在你的主租户或目录中。  您的应用程序的全局唯一 ID （应用程序或客户端 ID）。  然后，你可以在门户中添加机密或证书和作用域，以使你的应用工作、在登录对话框中自定义应用的品牌等。

如果在门户中注册应用程序，则会在你的 home 租户中自动创建应用程序对象和服务主体对象。  如果使用 Microsoft Graph Api 注册/创建应用程序，则创建服务主体对象是一个单独的步骤。

## <a name="application-object"></a>应用程序对象
Azure AD 应用程序由其唯一一个应用程序对象来定义，该对象位于应用程序注册到的 Azure AD 租户（称为应用程序的 "home" 租户）中。  应用程序对象用作模板或蓝图，用于创建一个或多个服务主体对象。  在使用应用程序的每个租户中创建一个服务主体。 与面向对象的编程中的类类似，应用程序对象具有一些静态属性，这些属性将应用于所有已创建的服务主体（或应用程序实例）。 

应用程序对象描述了应用程序的三个方面：服务如何发出令牌来访问应用程序、应用程序可能需要访问的资源以及应用程序可以执行的操作。 

[Azure 门户][AZURE-Portal]中的 "**应用注册**" 边栏选项卡用于列出和管理 home 租户中的应用程序对象。

Microsoft Graph [Application 实体][MS-Graph-App-Entity]定义应用程序对象属性的架构。

## <a name="service-principal-object"></a>服务主体对象
若要访问受 Azure AD 租户保护的资源，需要访问的实体必须由安全主体来表示。 这同时适用于用户（用户主体）和应用程序（服务主体）。 安全主体定义 Azure AD 租户中用户/应用程序的访问策略和权限。 这样便可实现核心功能，如在登录时对用户/应用程序进行身份验证，在访问资源时进行授权。

服务主体是单个租户或目录中全局应用程序对象的本地表示形式或应用程序实例。 服务主体是从应用程序对象创建的具体实例，并从该应用程序对象继承某些属性。  在使用应用程序的每个租户中创建一个服务主体，并引用全局唯一的应用程序对象。  服务主体对象定义应用可在特定租户中实际执行的操作，可访问应用的用户以及应用可以访问的资源。 

当应用程序被授予了对租户中资源的访问权限时（根据注册或[许可](developer-glossary.md#consent)），将创建一个服务主体对象。 你还可以使用[Azure PowerShell](howto-authenticate-service-principal-powershell.md)、Azure CLI、 [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http)、 [Azure 门户][AZURE-Portal]和其他工具在租户中创建服务主体对象。  使用门户时，会在注册应用程序时自动创建服务主体。

门户中的 "**企业应用程序**" 边栏选项卡用于列出和管理租户中的服务主体。 你可以看到服务主体的权限、用户许可权限、用户已完成同意、登录信息等。

Microsoft Graph [ServicePrincipal 实体][MS-Graph-Sp-Entity]定义服务主体对象属性的架构。

## <a name="relationship-between-application-objects-and-service-principals"></a>应用程序对象与服务主体之间的关系

应用程序对象是应用程序的*全局*表示形式，可用于所有租户，服务主体是在特定租户中使用的*本地*表示形式。

应用程序对象用作模板，常见属性和默认属性从其中*派生*，以便在创建相应服务主体对象时使用。 因此，应用程序对象与软件应用程序存在 1 对 1 关系，而与其对应的服务主体对象存在 1 对多关系。

必须在将使用应用程序的每个租户中创建服务主体，让它能够建立用于登录和/或访问受租户保护的资源的标识。 单租户应用程序只有一个服务主体（在其宿主租户中），在应用程序注册期间创建并被允许使用。 多租户 Web 应用程序/API 还会在租户中的某个用户已同意使用它的每个租户中创建服务主体。

> [!NOTE]
> 对应用程序对象所做的任何更改也只反映在该对象在应用程序宿主租户（其注册所在的租户）的服务主体对象中。 对于多租户应用程序，在通过[应用程序访问面板](https://myapps.microsoft.com)删除该访问权限并重新授予访问权限之前，对应用程序对象所做的更改不会反映在任何使用者租户的服务主体对象中。
>
> 另请注意，默认情况下本机应用程序注册为多租户应用程序。

## <a name="example"></a>示例

下图演示了应用程序的应用程序对象和对应的服务主体对象之间的关系，其上下文是在名为 **HR 应用**的示例多租户应用程序中。 此示例方案中有三个 Azure AD 租户：

- **Adatum** -开发**HR 应用**的公司使用的租户
- **Contoso** -contoso 组织使用的租户，即**HR 应用**的使用者
- **Fabrikam** -fabrikam 组织使用的租户，它也使用**HR 应用**

![应用对象与服务主体对象之间的关系](./media/app-objects-and-service-principals/application-objects-relationship.svg)

在此示例方案中：

| 步骤 | 说明 |
|------|-------------|
| 1    | 是在应用程序的宿主租户中创建应用程序对象和服务主体对象的过程。 |
| 2    | 当 Contoso 和 Fabrikam 的管理员完成同意并向应用程序授予访问权限时，会在其公司的 Azure AD 租户中创建服务主体对象，并向其分配管理员所授予的权限。 另请注意，HR 应用可能配置/设计为允许由用户同意以供个人使用。 |
| 3    | HR 应用程序的使用者租户（例如 Contoso 和 Fabrikam）各有自己的服务主体对象。 每个对象代表其在运行时使用的应用程序实例，该实例受相关管理员同意的权限控制。 |

## <a name="next-steps"></a>后续步骤

- 可以使用 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)查询应用程序和服务主体对象。
- 可以使用 Microsoft Graph API、[Azure 门户的][AZURE-Portal]应用程序清单编辑器或 [Azure AD PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) 访问应用程序的应用程序对象（由其 OData [Application 实体][MS-Graph-App-Entity]表示）。
- 可以通过 Microsoft Graph API 或 [Azure AD PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) 访问应用程序的服务主体对象（由其 OData [ServicePrincipal 实体][MS-Graph-Sp-Entity]表示）。

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
