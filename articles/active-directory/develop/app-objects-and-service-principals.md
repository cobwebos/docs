---
title: 应用和 Azure AD 中的服务主体 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Azure Active Directory 中应用程序对象与服务主体对象之间的关系。
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: db25e0a9ebe01a45c594fe214efcdd3551cf6c14
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449368"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory 中的应用程序对象和服务主体对象

本文介绍 Azure Active Directory 中的应用程序注册、应用程序对象和服务主体：它们是什么、它们的使用方式以及它们彼此之间的关系。 还展示了多租户示例方案，用于说明应用程序的应用程序对象和对应的服务主体对象之间的关系。

## <a name="application-registration"></a>应用程序注册
为了将标识和访问管理功能委托给 Azure AD，应用程序必须使用 Azure AD [租户](developer-glossary.md#tenant)进行注册。 将应用程序注册到 Azure AD 时，需要创建应用程序的标识配置，使其能够与 Azure AD 集成。 在 [Azure 门户][AZURE-Portal]中注册应用时，可以选择单租户（只能在自己的租户中访问）或多租户（可在其他租户中访问），也可以选择设置重定向 URI （将访问令牌发送到的位置）。

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="Azure 门户的“注册应用程序”窗格的屏幕截图":::

完成应用注册后，你将拥有应用（应用程序对象）的全局唯一实例，该实例存在于你的主租户或目录中。  而且你的应用拥有全局唯一 ID（应用或客户端 ID）。  然后，在门户中，你便可以添加机密或证书和作用域以使应用正常工作，在登录对话框中自定义应用的品牌等等。

如果在门户中注册应用程序，会在主租户中自动创建应用程序对象以及服务主体对象。  如果使用 Microsoft Graph API 注册/创建应用程序，则通过一个单独步骤创建服务主体对象。

## <a name="application-object"></a>应用程序对象
Azure AD 应用程序由其唯一一个应用程序对象进行定义，该对象位于应用程序注册到 Azure AD 租户（称为应用程序的“宿主”租户）中。  使用了一个应用程序对象来充当模板或蓝图，用于创建一个或多个服务主体对象。  在使用应用程序的每个租户中创建了一个服务主体。 与面向对象的编程中的类类似，应用程序对象具有一些静态属性，应用于所有已创建的服务主体（或应用程序实例）。

该应用程序对象描述应用程序的三个方面：服务如何发出令牌来访问应用程序、应用程序可能需要访问的资源以及应用程序可以执行的操作。

[Azure 门户][AZURE-Portal]中的“应用注册”边栏选项卡用于在主租户中列出和管理应用程序对象。

![应用注册边栏选项卡](./media/app-objects-and-service-principals/app-registrations-blade.png)

Microsoft Graph [Application 实体][MS-Graph-App-Entity]定义应用程序对象属性的架构。

## <a name="service-principal-object"></a>服务主体对象
若要访问受 Azure AD 租户保护的资源，需要访问的实体必须由安全主体来表示。 此要求同时适用于用户（用户主体）和应用程序（服务主体）。 安全主体定义 Azure AD 租户中用户/应用程序的访问策略和权限。 这样便可实现核心功能，如在登录时对用户/应用程序进行身份验证，在访问资源时进行授权。

服务主体是单个租户或目录中某个全局应用程序对象的本地表示形式或应用程序实例。 服务主体是从应用程序对象中创建的具体实例，并从该应用程序对象继承某些属性。  服务主体是在使用应用程序的每个租户中创建的，并引用全局唯一应用对象。  服务主体对象定义应用可在特定租户中实际执行的操作、可访问应用的用户以及应用可访问的资源。

当应用程序被授予了对租户中资源的访问权限时（根据注册或[许可](developer-glossary.md#consent)），将创建一个服务主体对象。 你还可以使用 [Azure PowerShell](howto-authenticate-service-principal-powershell.md)、 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)、 [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http)、 [Azure 门户][AZURE-Portal]和其他工具在租户中创建服务主体对象。  如果使用门户，会在注册应用程序时自动创建服务主体。

门户中的“企业应用程序”边栏选项卡用于在租户中列出和管理服务主体。 可查看服务主体的权限、用户已同意的权限、提供了相应同意的用户、登录信息等等。

![企业应用边栏选项卡](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

Microsoft Graph [ServicePrincipal 实体][MS-Graph-Sp-Entity]定义服务主体对象属性的架构。

## <a name="relationship-between-application-objects-and-service-principals"></a>应用程序对象与服务主体之间的关系

应用程序对象是应用程序的全局表示形式，可供所有租户使用，而服务主体是本地表示形式，在特定租户中使用 。

应用程序对象用作模板，常见属性和默认属性从其中*派生*，以便在创建相应服务主体对象时使用。 因此，应用程序对象与软件应用程序存在 1 对 1 关系，而与其对应的服务主体对象存在 1 对多关系。

必须在将使用应用程序的每个租户中创建服务主体，让它能够建立用于登录和/或访问受租户保护的资源的标识。 单租户应用程序只有一个服务主体（在其宿主租户中），在应用程序注册期间创建并被允许使用。 多租户 Web 应用程序/API 还会在租户中的某个用户已同意使用它的每个租户中创建服务主体。

> [!NOTE]
> 对应用程序对象所做的任何更改也只反映在该对象在应用程序宿主租户（其注册所在的租户）的服务主体对象中。 对于多租户应用程序，在通过[应用程序访问面板](https://myapps.microsoft.com)删除该访问权限并重新授予访问权限之前，对应用程序对象所做的更改不会反映在任何使用者租户的服务主体对象中。
>
> 另请注意，默认情况下本机应用程序注册为多租户应用程序。

## <a name="example"></a>示例

下图演示了应用程序的应用程序对象和对应的服务主体对象之间的关系，其上下文是在名为 **HR 应用**的示例多租户应用程序中。 此示例方案中有三个 Azure AD 租户：

- **Adatum** - 开发 **HR 应用**的公司使用的租户
- **Contoso** - Contoso 组织使用的租户，即 **HR 应用**的使用者
- **Fabrikam** - Fabrikam 组织使用的租户，它也使用 **HR 应用**

![应用对象与服务主体对象之间的关系](./media/app-objects-and-service-principals/application-objects-relationship.svg)

在此示例方案中：

| 步骤 | 说明 |
|------|-------------|
| 1    | 是在应用程序的宿主租户中创建应用程序对象和服务主体对象的过程。 |
| 2    | 当 Contoso 和 Fabrikam 的管理员完成同意并向应用程序授予访问权限时，会在其公司的 Azure AD 租户中创建服务主体对象，并向其分配管理员所授予的权限。 另请注意，HR 应用可能配置/设计为允许由用户同意以供个人使用。 |
| 3    | HR 应用程序的使用者租户（例如 Contoso 和 Fabrikam）各有自己的服务主体对象。 每个对象代表其在运行时使用的应用程序实例，该实例受相关管理员同意的权限控制。 |

## <a name="next-steps"></a>后续步骤

- 可以使用 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)查询应用程序和服务主体对象。
- 可以使用 Microsoft Graph API、[Azure 门户的][AZURE-Portal]应用程序清单编辑器或 [Azure AD PowerShell cmdlet](/powershell/azure/?view=azureadps-2.0) 访问应用程序的应用程序对象（由其 OData [Application 实体][MS-Graph-App-Entity]表示）。
- 可以通过 Microsoft Graph API 或 [Azure AD PowerShell cmdlet](/powershell/azure/?view=azureadps-2.0) 访问应用程序的服务主体对象（由其 OData [ServicePrincipal 实体][MS-Graph-Sp-Entity]表示）。

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com