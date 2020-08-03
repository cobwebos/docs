---
title: Azure AD 域服务的资源林概念 | Microsoft Docs
description: 了解 Azure Active Directory 域服务中的资源林，以及它们如何在混合环境中的有限用户身份验证选项或安全问题方面使组织受益。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 62a2ffeea1d15a16c4ec4aa6a2b88c8e34763064
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87480401"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory 的资源林概念和功能

Azure Active Directory 域服务 (Azure AD DS) 为旧的本地业务线应用程序提供登录体验。 本地和云用户的用户、组和密码哈希会同步到 Azure AD DS 托管域。 这些同步密码哈希可为用户提供一组可用于本地 AD DS、Office 365 和 Azure Active Directory 的凭据。

尽管安全并提供额外的安全好处，但某些组织无法将这些用户密码哈希同步到 Azure AD 或 Azure AD DS。 组织中的用户可能不知道其密码，因为他们只使用智能卡身份验证。 这些限制可防止某些组织使用 Azure AD DS 将本地经典应用程序直接迁移到 Azure。

若有满足这些需求和限制，可以创建使用资源林的托管域。 本文介绍什么是林，以及它们如何信任其他资源来提供安全的身份验证方法。

## <a name="what-are-forests"></a>什么是林？

林是 Active Directory 域服务 (AD DS) 用来对一个或多个域进行分组的逻辑构造 。 域随后会存储用户或组的对象，并提供身份验证服务。

在 Azure AD DS 托管域中，林只包含一个域。 本地 AD DS 林通常包含许多域。 在大型组织中，特别是在合并和收购之后，最终可能会得到多个本地林，每个林又包含多个域。

默认情况下，托管域是作为用户林创建的。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 用户帐户可以直接通过托管域进行身份验证，以便执行相关操作，例如登录到已加入域的 VM。 当可以同步密码哈希，并且用户不使用独占登录方法（如智能卡身份验证）时，用户林可发挥作用。

在托管域*资源*林中，用户从本地 AD DS 对单向林*信任*进行身份验证。 采用此方法时，用户对象和密码哈希不会同步到托管域。 用户对象和凭据仅存在于本地 AD DS 中。 此方法使企业可以在 Azure 中托管依赖于经典身份验证（如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，不过可消除任何身份验证问题或疑虑。

资源林还提供一次一个组件地直接迁移应用程序的功能。 许多旧的本地应用程序是多层的，通常使用 Web 服务器或前端以及许多数据库相关组件。 这些层使得难以在一步中将整个应用程序直接迁移到云。 利用资源林，可以分阶段地将应用程序提升到云，这样可以更轻松地将应用程序移动到 Azure。

## <a name="what-are-trusts"></a>什么是信任？

具有多个域的组织通常需要用户可访问另一个域中的共享资源。 访问这些共享资源需要一个域中的用户向另一个域进行身份验证。 若要在不同域中的客户端与服务器之间提供这些身份验证和授权功能，两个域之间必须存在信任。

对于域信任，每个域的身份验证机制信任来自其他域的身份验证。 信任通过验证传入的身份验证请求是否来自受信任的机构（受信任的域），来帮助提供对资源域（信任域）中共享资源的受控访问 。 信任充当桥梁，只允许经验证的身份验证请求在域之间传输。

信任传递身份验证请求的方式取决于其配置方式。 信任可以使用下列方法之一进行配置：

* 单向 - 提供从受信任的域到信任域中资源中的访问的权限权限。
* 双向 - 提供从每个域到其他域中的资源的访问权限。

还可以通过以下方式之一配置信任，以处理其他信任关系：

* 不可传递 - 信任仅存在于两个信任合作伙伴域之间。
* 可传递 - 信任自动扩展到任一合作伙伴所信任的任何其他域。

在某些情况下，创建域时会自动建立信任关系。 在其他情况下，必须选择一种信任类型并显式建立适当的关系。 使用的特定信任类型和这些信任关系的结构取决于 AD DS 目录的组织方式，以及网络上是否共存不同版本的 Windows。

## <a name="trusts-between-two-forests"></a>两个林之间的信任

通过手动创建单向或双向林信任，可以将单个林中的域信任扩展到其他林。 林信任是仅存在于林根域与辅助林根域之间的可传递信任。

* 单向林信任允许一个林中的所有用户信任另一个林中的所有域。
* 双向林信任在两个林中的每个域之间组成可传递信任关系。

林信任的传递性仅限于两个林合作伙伴。 林信任不会扩展到任一合作伙伴所信任的附加林。

![从 Azure AD DS 到本地 AD DS 的林信任关系图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可以根据组织的 AD DS 结构创建不同的域和林信任配置。 Azure AD DS 仅支持单向林信任。 在此配置中，托管域中的资源可以信任本地林中的所有域。

## <a name="supporting-technology-for-trusts"></a>适用于信任的支持技术

信任使用各种服务和功能（如 DNS，用于定位合作林中的域控制器）。 信任还依赖于 NTLM 和 Kerberos 身份验证协议以及基于 Windows 的授权和访问控制机制，以帮助跨 AD DS 域和林提供安全的通信基础结构。 以下服务和功能可帮助支持成功的信任关系。

### <a name="dns"></a>DNS

AD DS 需要将 DNS 用于域控制器 (DC) 定位和命名。 为了使 AD DS 成功工作，提供了来自 DNS 的以下支持：

* 名称解析服务，使网络主机和服务可以定位 DC。
* 命名结构，使企业可以在其目录服务域的名称中反映其组织结构。

通常会部署镜像 AD DS 域命名空间的 DNS 域命名空间。 如果在 AD DS 部署之前存在现有的 DNS 命名空间，则通常会为 AD DS 分区 DNS 命名空间，并创建 AD DS 林根的 DNS 子域和委派。 然后，将为每个 AD DS 子域添加其他 DNS 域名。

DNS 还用于支持 AD DS Dc 的位置。 DNS 区域用 DNS 资源记录填充，使网络主机和服务能够查找 AD DS Dc。

### <a name="applications-and-net-logon"></a>应用程序和网络登录

应用程序和网络登录服务都是 Windows 分布式安全通道模型的组件。 与 Windows Server 和 AD DS 集成的应用程序使用身份验证协议与 Net Logon 服务进行通信，以便可以建立安全的路径，以便通过身份验证进行身份验证。

### <a name="authentication-protocols"></a>身份验证协议

AD DS Dc 使用以下协议之一对用户和应用程序进行身份验证：

* Kerberos 版本 5 身份验证协议
    * Kerberos 版本 5 协议是运行 Windows 并支持第三方操作系统的本地计算机所使用的默认身份验证协议。 此协议在 RFC 1510 中指定，并与 AD DS、服务器消息块（SMB）、HTTP 和远程过程调用（RPC）以及使用这些协议的客户端和服务器应用程序完全集成。
    * 使用 Kerberos 协议时，服务器不必联系 DC。 相反，客户端会通过从服务器帐户域中的 DC 请求票证来获取用于服务器的票证。 服务器随后会验证票证，而不会咨询任何其他颁发机构。
    * 如果事务中涉及的任何计算机都不支持 Kerberos 版本 5 协议，则使用 NTLM 协议。

* NTLM 身份验证协议
    * NTLM 协议是较早操作系统所使用的经典网络身份验证协议。 出于兼容性原因，AD DS 域使用它来处理来自为早期基于 Windows 的客户端和服务器以及第三方操作系统设计的应用程序的网络身份验证请求。
    * 当在客户端与服务器之间使用 NTLM 协议时，服务器必须联系 DC 上的域身份验证服务以验证客户端凭据。 服务器通过将客户端凭据转发到客户端帐户域中的 DC 来对客户端进行身份验证。
    * 当两个 AD DS 域或林连接到一个信任时，可以路由使用这些协议发出的身份验证请求，以提供对这两个林中的资源的访问权限。

## <a name="authorization-and-access-control"></a>授权和访问控制

授权和信任技术共同提供跨 AD DS 域或林的安全通信基础结构。 授权确定用户对域中的资源所拥有的访问权限级别。 信任可帮助进行用户的跨域授权，具体方法是提供用于在其他域中对用户进行身份验证的路径，以便可以授权用户对这些域中的共享资源的请求。

当信任域中发出的身份验证请求经过受信任的域验证时，它会传递到目标资源。 目标资源随后会根据其访问控制配置来确定是否对受信任的域中的用户、服务或计算机发出的特定请求授权。

信任提供此机制来验证传递到信任域的身份验证请求。 资源计算机上的访问控制机制确定向受信任的域中的请求者授予的最终访问权限级别。

## <a name="next-steps"></a>后续步骤

若要了解有关信任的详细信息，请参阅[林信任如何在 Azure AD DS 中发挥作用？][concepts-trust]

若要开始使用资源林创建托管域，请参阅[创建和配置 Azure AD DS 托管域][tutorial-create-advanced]。 然后，你可以[创建到本地域的出站林信任][create-forest-trust]。

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
