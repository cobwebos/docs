---
title: "Azure Active Directory 混合标识设计注意事项 - 确定混合标识生命周期采用策略 | Microsoft 文档"
description: "帮助依据每个生命周期阶段的可用选项定义混合标识管理任务。"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: ef8c4a0f672af2540f071f40eac77dc881eb31ac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>确定混合标识生命周期采用策略
蝻进在本任务中，将定义混合标识解决方案的标识管理策略，以满足在[确定混合标识管理任务](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)中定义的业务需求。

若要依据本步骤前面介绍的端到端标识生命周期定义混合标识管理任务，需要考虑每个生命周期阶段的可用选项。

## <a name="access-management-and-provisioning"></a>访问管理和预配
良好的帐户访问管理解决方案使组织可以精确跟踪整个组织中谁有权访问哪些信息。

访问控制是集中式、单点预配系统的一个重要功能。 除了保护敏感信息，访问控制还将公开具有未经批准的授权或已无存在必要的现有帐户。 预配系统将帐户信息与拥有帐户的用户相关的授权信息联系起来，可以控制过时的帐户。 授权的用户标识信息通常保留在人力资源部门的数据库和目录中。

复杂 IT 企业中的帐户包括数百个用于定义授权的参数，这些详细信息可通过预配系统进行控制。 可以使用权威源提供的数据标识新用户。 访问权限请求批准功能启动为其批准（或拒绝）资源预配的过程。

| 生命周期管理阶段 | 本地 | 云 | 混合 |
| --- | --- | --- | --- |
| 帐户管理和预配 |使用 Active Directory® 域服务 (AD DS) 服务器角色，为用户和资源管理创建可伸缩、安全和可管理的基础结构，以及为支持目录的应用程序（如 Microsoft® Exchange Server）提供支持。 <br><br> [使用标识管理器在 AD DS 中预配组](https://technet.microsoft.com/library/ff686261.aspx) <br>[在 AD DS 中预配用户](https://technet.microsoft.com/library/ff686263.aspx) <br><br> 出于安全目的，管理员可以使用访问控制来管理对共享资源的用户访问。 在 Active Directory 中，通过设置不同的对象访问级别或权限等级，例如，完全控制、写入、读取或无访问权限，在对象级管理访问控制。 在 Active Directory 中，访问控制定义不同用户使用 Active Directory 对象的方式。 默认情况下，Active Directory 中对象的权限设置为最安全的设置。 |必须为想要访问 Microsoft 云服务的每个用户创建帐户。 还可以更改用户帐户或删除不再需要的用户帐户。 默认情况下，用户不具有管理员权限，但可以选择给他们分配权限。 有关详细信息，请参阅[在 Azure AD 中管理用户](active-directory-create-users.md)。 <br><br> Azure Active Directory 的主要功能之一是管理对资源的访问。 这些资源可以是目录的一部分（例如用于通过目录中的角色管理对象的权限）、目录外部的资源（例如 SaaS 应用程序、Azure 服务以及 SharePoint 站点）或者本地资源。 <br><br> Azure Active Directory 访问管理解决方案的核心是安全组。 资源所有者（或目录管理员）可以分配组，以提供对其拥有的资源的特定访问权限。 组成员会获得访问权限，而资源所有者可将组成员列表管理权限委托给其他人，例如，部门经理或支持管理员<br> <br> “在 Azure AD 中管理组”主题提供了通过组管理访问权限的更多信息。 |通过同步和联合身份验证将 Active Directory 标识扩展到云中 |

## <a name="role-based-access-control"></a>基于角色的访问控制
基于角色的访问控制 (RBAC) 使用角色和预配策略来评估、测试并强制执行业务流程和向用户授予访问权限的规则。 主要管理员创建预配策略并为用户分配角色，从而定义这些角色访问资源的权限集。 RBAC 扩展了标识管理解决方案，以便使用基于软件的流程并减少在预配过程中的用户手动交互。
Azure AD RBAC 使公司能够限制有权访问 Azure 管理门户的个人可以执行的操作数。 使用 RBAC 控制对门户的访问时，IT 管理员可通过下列访问管理方式委派访问权限：

* **基于组的角色分配**：为可从本地 Active Directory 同步的 Azure AD 组分配访问权限。 这使你能够充分利用组织在工具和组管理过程中已有的现有投资。 也可以使用 Azure AD 高级版中的委派组管理功能。
* **充分利用 Azure 中的内置角色**：可以使用三个角色 - 所有者、参与者和阅读人员，以确保用户和组仅具有完成工作所需任务的权限。
* **对资源的细致访问**：针对特定的订阅、资源组或单独的 Azure 资源（例如，网站或数据库），可以将角色分配给用户和组。 这样，可以确保用户有权访问所有所需资源，并且无权访问不需要管理的资源。

## <a name="provisioning-and-other-customization-options"></a>预配和其他自定义选项
团队可以根据业务规划和业务要求来决定自定义标识解决方案的程度。 例如，一家大型企业可能需要根据时间线针对工作流和自定义适配器分阶段实施计划，以增量方式预配跨地域广泛使用的应用程序。 成功测试之后，另一个自定义计划可能提供两个或更多要在整个组织范围内预配的应用程序。 可以自定义用户-应用程序交互，还可以更改资源预配过程以适应自动化预配。

通过取消预配，可以删除某个服务或组件。 例如，取消帐户预配意味着从资源中删除该帐户。

资源预配的混合模型结合了请求和基于角色的方法，这二者都受 Azure AD 支持。 对于雇员或托管系统的子集，企业可以使用基于角色的分配使访问自动化。 企业还可以通过基于要求模型处理所有其他访问请求或异常。 一些企业可能最初使用手动分配，逐渐发展为使用混合模型，并计划在未来使用完全基于角色的部署。

其他公司出于商业原因可能认为实现完全基于角色的预配不切实际，从而将混合方法作为期望的目标。 另外还有一些公司可能满足于仅使用基于请求的预配，不想为定义和管理基于角色的自动化预配策略投入额外精力。

## <a name="license-management"></a>许可证管理
Azure AD 中基于组的许可证管理允许管理员将用户分配到安全组，Azure AD 自动将许可证分配给组内的所有成员。 如果随后向组中添加用户或从组中删除用户，将相应地自动分配或删除许可证。

可以使用从本地 AD 同步的组或在 Azure AD 中进行管理。 将此方法与 Azure AD 高级版自助服务组管理配对，可以轻松将许可证分配委托给相应的决策者。 请尽管放心，许可证冲突和缺少位置数据等类似问题会自动解决。

## <a name="self-regulating-user-administration"></a>自我调节的用户管理
当组织开始跨所有内部组织预配资源时，将实现自我调节的用户管理功能。 可以实现跨组织边界预配用户的优势和好处。 在此环境中，用户状态更改将跨组织边界和地理位置自动反映在访问权限中。 可以降低预配成本以及简化访问和审批流程。 此实现可以在组织中为端到端的访问管理实现基于角色的访问控制的全部潜能。 通过管理用户预配的自动化过程，可以降低管理成本。 可以通过自动化安全策略执行改善安全性，并为庞大的用户群体简化和集中用户生命周期管理和资源预配。

> [!NOTE]
> 有关详细信息，请参阅“为自助服务应用程序访问管理设置 Azure AD”
> 
> 

基于许可证（基于权利）的 Azure AD 服务的工作原理是在 Azure AD 目录/服务租户中激活订阅。 一旦激活订阅，服务功能就可以由目录/服务管理员管理，并可由许可的用户使用。 有关详细信息，请参阅“Azure AD 授权的工作原理？”
与其他第三方提供程序集成

Azure Active Directory 为数千种 SaaS 应用程序和本地 Web 应用程序提供单一登录和增强的应用程序访问安全性。 有关受支持 SaaS 应用程序的 Azure Active Directory 应用程序库的详细列表，请参阅“Azure Active Directory 联合身份验证兼容性列表：可用来实现单一登录的第三方标识提供程序”

## <a name="define-synchronization-management"></a>定义同步管理
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 通过这种集成，用户和组织可以享受到以下好处：

* 组织可以利用 Windows Server Active Directory 并连接到 Azure Active Directory，向用户提供跨本地或云服务的通用混合标识。
* 管理员可以根据应用程序资源、设备和用户标识、网络位置及 Multi-Factor Authentication，提供条件性访问。
* 用户可以通过 Azure AD 中的帐户，在 Office 365、Intune、SaaS 应用和第三方应用程序中利用其通用标识。
* 开发人员可以将应用程序集成到 Active Directory 本地，或者将基于云的应用程序集成到 Azure，以利用通用标识模型构建应用程序

下图对标识同步过程的高级视图进行举例说明。

![](./media/hybrid-id-design-considerations/identitysync.png)

标识同步过程

查看下表以比较同步选项：

| 同步管理选项 | 优点 | 缺点 |
| --- | --- | --- |
| 基于同步（通过 DirSync 或 AADConnect） |从本地和云同步的用户和组 <br>  **策略控制**：可通过 Active Directory 设置帐户策略，这使得管理员无需在云中执行附加任务的情况下，就能够管理密码策略、工作站、限制、锁定控制等。  <br>  **访问控制**：可限制对云服务的访问，以便通过企业环境和/或联机服务器访问这些服务。 <br>  减少支持呼叫：用户需要记住的密码数量越少，就越不容易忘记它们。 <br>  安全性：由于单一登录使用的所有服务器和服务都在本地掌握和控制，因此用户标识和信息可受到保护。 <br>  支持强身份验证：对于云服务，可以使用强身份验证（也称为双重身份验证）。 但是，如果使用强身份验证，必须使用单一登录。 | |
| 基于联合身份验证（通过 AD FS） |受安全令牌服务 (STS) 支持。 使用 Microsoft 云服务配置 STS 以提供单一登录访问权限时，会在本地 STS和在 Azure AD 租户中指定的联合域之间创建联合信任。 <br> 允许最终用户使用同一组凭据来获得对多个资源的访问权限 <br>最终用户不需要维护多组凭据。 但是，用户必须为每个参与资源以及受支持的 B2B 和 B2C 方案提供凭据。 |需要专业人员部署和维护专用的本地 AD FS 服务器。 如果计划将 AD FS 用于 STS，强身份验证的使用将受到限制。 有关详细信息，请参阅[为 AD FS 2.0 配置高级选项](http://go.microsoft.com/fwlink/?linkid=235649)。 |

> [!NOTE]
> 有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。
> 
> 

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)

