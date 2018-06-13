---
title: 如何以及为何将应用程序添加到 Azure Active Directory
description: 为何要将应用程序添加到 Azure AD，如何添加？
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: c9ebfcba59e3f46fb30f4cd2402ec4ebb606f6d0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156164"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>如何以及为何将应用程序添加到 Azure AD
Azure AD 中的应用程序有两种表示形式： 
* [应用程序对象](active-directory-application-objects.md#application-object) - 尽管存在一些[例外情况](#notes-and-exceptions)，但可将这些对象视为应用程序的定义。
* [服务主体](active-directory-application-objects.md#service-principal-object) - 可被视为应用程序的实例。 服务主体通常引用应用程序对象，一个应用程序对象可由不同目录中的多个服务主体引用。

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>什么是应用程序对象，它们来自何处？
[应用程序对象](active-directory-application-objects.md#application-object)（可以在 Azure 门户中通过[应用程序注册](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)体验进行管理）描述 Azure AD 中的应用程序，可被视为应用程序的定义，使服务能够知道如何根据应用程序的设置，向该应用程序颁发令牌。 应用程序对象只在其主目录中存在，即使它是支持其他目录中服务主体的多租户应用程序。 应用程序对象可以包括以下任何项（以及此处未提到的其他信息）：
* 名称、徽标和发布者
* 回复 URL
* 机密（用于对应用程序进行身份验证的对称和/或非对称密钥）
* API 依赖关系 (OAuth)
* 发布的 API/资源/范围 (OAuth)
* 应用程序角色 (RBAC)
* SSO 元数据和配置
* 用户设置元数据和配置
* 代理元数据和配置

可通过多种途径创建应用程序对象，包括：
* Azure 门户中的应用程序注册
* 使用 Visual Studio 创建新应用程序，并将其配置为使用 Azure AD 身份验证
* 当管理员从应用库添加应用程序时（这也会创建服务主体）
* 使用 Microsoft 图形 API、Azure AD 图形 API 或 PowerShell 创建新应用程序
* 其他许多途径，包括 Azure 中的各种开发人员体验，以及开发人员中心的 API 资源管理器体验

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>什么是服务主体，它们来自何处？
[服务主体](active-directory-application-objects.md#service-principal-object)（可以通过[企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)体验进行管理）是实际控制与 Azure AD 相连接的应用程序的对象，可被视为目录中应用程序的实例。 任意给定应用程序最多只能有一个应用程序对象（在“home”目录中注册），此外，可以有一个或多个服务主体对象，这些对象表示运行该应用程序的每个目录中的应用程序实例。 

服务主体可以包括：

* 通过应用程序 ID 属性对应用程序对象的反向引用
* 本地用户和组应用程序角色分配的记录
* 授予应用程序的本地用户和管理员权限的记录
  * 例如：应用程序访问特定用户电子邮件的权限
* 本地策略（包括条件访问策略）的记录
* 应用程序的备用本地设置的记录
  * 声明转换规则
  * 属性映射（用户设置）
  * 目录特定的应用角色（如果应用程序支持自定义角色）
  * 目录特定的名称或徽标

与应用程序对象一样，可通过多种途径创建服务主体，包括：

* 当用户登录到与 Azure AD 集成的第三方应用程序时
  * 在登录期间，系统要求用户向应用程序授予访问其配置文件的权限和其他权限。 第一个授权者导致生成一个服务主体，表示要添加到目录中的应用程序。
* 当用户登录到 [Office 365](http://products.office.com/) 等 Microsoft 联机服务时
  * 订阅 Office 365 或开始试用时，会在目录中创建一个或多个服务主体，表示传递所有与 Office 365 关联的功能的各种服务。
  * 某些 Office 365 服务（如 SharePoint）会不断地创建服务主体，以允许在组件（包括工作流）之间进行安全通信。
* 当管理员从应用库添加应用程序时（这也会创建基础应用对象）
* 添加一个应用程序以使用 [Azure AD 应用程序代理](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* 连接应用程序，以使用 SAML 或密码单一登录 (SSO) 进行单一登录
* 通过 Azure AD 图形 API 或 PowerShell 以编程方式创建

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>如何将应用程序对象与服务主体彼此相关？
应用程序的主目录中包含一个应用程序对象，该对象由运行该应用程序的每个目录（包括该应用程序的主目录）中的一个或多个服务主体引用。
![演示应用程序对象和服务主体如何彼此交互以及与 Azure AD 实例交互的关系图。][apps_service_principals_directory]

在上面的关系图中，Microsoft 在内部维护两个用于发布应用程序的目录（左侧显示）：

* 一个目录用于 Microsoft 应用程序（Microsoft 服务目录）
* 一个目录用于预先集成的第三方应用程序（应用库目录）

与 Azure AD 集成的应用程序发布者/供应商需有一个发布目录（在右侧显示为“某个 SaaS 目录”）。

自行添加的应用程序（在关系图中显示为**你的应用**）包括：

* 开发的应用（与 Azure AD 集成）
* 为了进行单一登录而连接的应用程序
* 使用 Azure AD 应用程序代理发布的应用

### <a name="notes-and-exceptions"></a>备注和例外情况
* 并非所有服务主体都会往后指向应用程序对象。 最初生成 Azure AD 时，提供给应用程序的服务存在更多的限制，使用服务主体便足以建立应用程序标识。 原始服务主体在形式上更接近于 Windows Server Active Directory 服务帐户。 出于此原因，仍可以通过不同的途径创建服务主体（例如使用 Azure AD PowerShell），而无需首先创建应用程序对象。 Azure AD 图形 API 在创建服务主体之前需要一个应用程序对象。
* 上述信息当前并非全部都是以编程方式公开的。 只能在 UI 中使用以下功能：
  * 声明转换规则
  * 属性映射（用户设置）
* 有关服务主体和应用程序对象的详细信息，请参阅 Azure AD 图形 REST API 参考文档：
  * [应用程序](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [服务主体](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>应用程序为何要与 Azure AD 集成？
应用程序将添加到 Azure AD，以利用 Azure AD 提供的一个或多个服务，包括：

* 应用程序身份验证和授权
* 用户身份验证和授权
* 使用联合身份验证或密码的 SSO
* 用户预配和同步
* 基于角色的访问控制 - 使用目录定义应用程序角色，以便在应用程序中执行基于角色的授权检查
* OAuth 授权服务 - Office 365 和其他 Microsoft 应用程序使用这些服务来授予对 API/资源的访问权限
* 应用程序发布和代理 - 将应用程序从专用网络发布到 Internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>谁有权向我的 Azure AD 实例添加应用程序？
尽管有些任务只能由全局管理员执行（例如，从应用库添加应用程序，以及将应用程序配置为使用应用程序代理），但默认情况下，目录中的所有用户都有权添加他们正在开发的应用程序，并通过许可来决定要共享哪些应用程序/授予对其组织数据的访问权限。 当目录中的第一个用户登录到应用程序并授予许可时，会在租户中创建一个服务主体；否则，许可授予信息将存储在现有的服务主体中。

允许用户注册和许可应用程序最初听上去可能令人担忧，但请记住以下要点：


* 多年以来，应用程序一直可以利用 Windows Server Active Directory 进行用户身份验证，而无需在目录中注册或记录应用程序。 现在，组织提高了可见性，完全知道有多少应用程序正在使用目录以及为何使用目录。
* 将这些责任委托给用户消除了管理员驱动的应用程序发布和注册过程。 过去，在使用 Active Directory 联合身份验证服务 (ADFS) 时，管理员可能必须代表开发人员添加一个应用程序作为信赖方。 现在，开发人员可以自行解决问题。
* 用户为了业务目的使用其组织帐户登录到应用程序是一个好现象。 如果他们以后离开了组织，便会自动失去他们所用应用程序中帐户的访问权限。
* 记录与哪个应用程序共享了哪些数据是一个很好的做法。 数据的可流动性比以往更好，因此，明确记录哪个用户与哪些应用程序共享了哪些数据会很有用。
* 为 OAuth 使用 Azure AD 的 API 所有者将明确决定用户可向应用程序授予哪些权限，以及哪些权限需要管理员的许可。 只有管理员才能许可较大范围的且更重要的权限，而用户许可范围限制为用户自己的数据和功能。
* 当用户添加应用程序或允许应用程序访问其数据时，可以审核事件，使你能够在 Azure 门户中查看“审核报告”，以确定应用程序是如何添加到目录中的。

如果仍然想要阻止目录中的用户在未经管理员批准的情况下注册和登录应用程序，可以更改两项设置来关闭这些功能：

* 阻止用户自行许可应用程序：
  1. 在 Azure 门户中，转到[企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)下的“用户设置”部分。
  2. 将**用户可以自行许可访问公司数据的应用**设置为**否**。 
     请注意，如果决定关闭用户许可，则必须由管理员许可用户需要使用的任何新应用程序。
* 阻止用户注册其自己的应用程序：
  1. 在 Azure 门户中，转到“Azure Active Directory”下的[用户设置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)部分
  2. 将**用户可以注册应用程序**更改为**否**。

> [!NOTE]
> Microsoft 自身使用默认配置，允许用户自行注册应用程序和许可应用程序。

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

