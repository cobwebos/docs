---
title: Azure AD 域服务的资源林概念 |Microsoft Docs
description: 了解资源林 Azure Active Directory 域服务中的内容，以及如何在混合环境中使用有限的用户身份验证选项或安全注意事项来使组织受益。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233605"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的资源林概念和功能

Azure Active Directory 域服务（AD DS）为旧的本地业务线应用程序提供登录体验。 本地和云用户的用户、组和密码哈希将同步到 Azure AD DS 托管域。 这些同步密码哈希可为用户提供一组可用于本地 AD DS、Office 365 和 Azure Active Directory 的凭据。

尽管安全并提供额外的安全优势，但某些组织不能将这些用户密码哈希同步到 Azure AD 或 Azure AD DS。 组织中的用户可能不知道其密码，因为他们只使用智能卡身份验证。 这些限制可防止某些组织使用 Azure AD DS 将本地经典应用程序直接迁移到 Azure。

为了满足这些需求和限制，你可以创建使用资源林 Azure AD DS 托管域。 本文介绍了什么是林，以及它们如何信任其他资源来提供安全的身份验证方法。 Azure AD DS 资源林目前处于预览阶段。

> [!IMPORTANT]
> Azure AD DS 资源林目前不支持 Azure HDInsight 或 Azure 文件。 默认 Azure AD DS 用户林支持这两个附加服务。

## <a name="what-are-forests"></a>什么是林？

*林*是 Active Directory 域服务（AD DS）用于对一个或多个*域*进行分组的逻辑构造。 然后，域存储用户或组的对象，并提供身份验证服务。

在 Azure AD DS 中，林只包含一个域。 本地 AD DS 林通常包含多个域。 在大型组织中，特别是合并和收购后，最终可能会得到多个本地林，每个林都包含多个域。

默认情况下，将 Azure AD DS 托管域创建为*用户*林。 这种类型的林将同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的任何用户帐户。 用户帐户可以直接针对 Azure AD DS 托管域进行身份验证，例如登录到已加入域的 VM。 用户林可用于同步密码哈希，并且用户不使用类似于智能卡身份验证的专用登录方法。

在 Azure AD DS*资源*林中，用户从本地 AD DS 对单向林*信任*进行身份验证。 采用此方法时，用户对象和密码哈希不会同步到 Azure AD DS。 仅本地 AD DS 中存在用户对象和凭据。 此方法允许企业在 Azure 中托管依赖于经典身份验证（例如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，但会删除任何身份验证问题或问题。 Azure AD DS 资源林目前处于预览阶段。

资源林还提供了一次将应用程序提升到一个组件的功能。 许多旧的本地应用程序是多层的，通常使用 web 服务器、前端和许多与数据库相关的组件。 通过这些级别，可以一步将整个应用程序直接迁移到云。 利用资源林，可以分阶段地将应用程序提升到云，这样可以更轻松地将应用程序移动到 Azure。

## <a name="what-are-trusts"></a>什么是信任？

具有多个域的组织通常需要用户访问另一个域中的共享资源。 若要访问这些共享资源，需要一个域中的用户向另一个域进行身份验证。 若要在不同域中的客户端和服务器之间提供这些身份验证和授权功能，两个域之间必须存在*信任*。

对于域信任，每个域的身份验证机制信任来自其他域的身份验证。 通过验证传入身份验证请求是否来自受信任的颁发机构（*可信*域），信任有助于提供对资源域（*信任*域）中共享资源的受控访问权限。 信任充当桥，只允许验证的身份验证请求在域之间传输。

信任通过身份验证请求的方式取决于其配置方式。 可以通过以下方式之一配置信任：

* **单向-提供**从受信任域到信任域中资源的访问权限。
* **双向-提供**从每个域到另一个域中的资源的访问权限。

还可以通过以下方式之一配置信任来处理其他信任关系：

* **不可传递**-信任仅存在于两个信任伙伴域之间。
* 可**传递**信任自动扩展到合作伙伴信任的任何其他域。

在某些情况下，会在创建域时自动建立信任关系。 其他情况下，您必须选择一个信任类型并显式建立适当的关系。 使用的特定信任类型和这些信任关系的结构取决于 Active Directory 目录服务的组织方式，以及网络上是否共存不同版本的 Windows。

## <a name="trusts-between-two-forests"></a>两个林之间的信任

通过手动创建单向或双向林信任，可以将单个林中的域信任扩展到其他林。 林信任是只存在于目录林根级域和第二个目录林根级域之间的可传递信任。

* 单向林信任允许一个林中的所有用户信任另一个林中的所有域。
* 双向林信任形成两个林中每个域之间的可传递信任关系。

林信任的传递性仅限于两个林伙伴。 林信任并不会扩展到任何合作伙伴信任的其他林。

![从 Azure AD DS 到本地 AD DS 的林信任关系图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可以根据组织的 Active Directory 结构创建不同的域和林信任配置。 Azure AD DS 仅支持单向林信任。 在此配置中，Azure AD DS 中的资源可以信任本地林中的所有域。

## <a name="supporting-technology-for-trusts"></a>信任的支持技术

信任使用各种服务和功能，如 DNS 查找合作林中的域控制器。 信任还依赖于 NTLM 和 Kerberos 身份验证协议以及基于 Windows 的授权和访问控制机制，以帮助跨 Active Directory 域和林提供安全的通信基础结构。 以下服务和功能可帮助支持成功的信任关系。

### <a name="dns"></a>DNS

AD DS 需要 DNS 用于域控制器（DC）位置和命名。 为了使 AD DS 成功工作，提供了来自 DNS 的以下支持：

* 一种名称解析服务，它允许网络主机和服务定位 Dc。
* 一种命名结构，使企业可以在其目录服务域的名称中反映其组织结构。

通常会部署一个 DNS 域命名空间，该命名空间反映 AD DS 域命名空间。 如果在 AD DS 部署之前存在现有的 DNS 命名空间，则通常会为 Active Directory 分区 DNS 命名空间，并创建 Active Directory 林根的 DNS 子域和委派。 然后，将为每个 Active Directory 子域添加其他 DNS 域名。

DNS 还用于支持 Active Directory Dc 的位置。 DNS 区域用 DNS 资源记录填充，使网络主机和服务能够查找 Active Directory Dc。

### <a name="applications-and-net-logon"></a>应用程序和网络登录

应用程序和 Net Logon 服务都是 Windows distributed security 信道模型的组成部分。 与 Windows Server 和 Active Directory 集成的应用程序使用身份验证协议与 Net Logon 服务进行通信，以便可以建立安全的路径，以便通过身份验证进行身份验证。

### <a name="authentication-protocols"></a>身份验证协议

Active Directory Dc 使用以下协议之一对用户和应用程序进行身份验证：

* **Kerberos 版本5身份验证协议**
    * Kerberos 版本5协议是默认的身份验证协议，它由运行 Windows 并支持第三方操作系统的本地计算机使用。 此协议在 RFC 1510 中指定，并与 Active Directory、服务器消息块（SMB）、HTTP 和远程过程调用（RPC）以及使用这些协议的客户端和服务器应用程序完全集成。
    * 使用 Kerberos 协议时，服务器不必联系 DC。 相反，客户端会通过从服务器帐户域中的 DC 请求一个票证来获取服务器的票证。 然后，服务器将验证票证，而不会咨询任何其他颁发机构。
    * 如果事务中涉及的任何计算机不支持 Kerberos 版本5协议，则使用 NTLM 协议。

* **NTLM 身份验证协议**
    * NTLM 协议是早期版本的操作系统使用的经典网络身份验证协议。 出于兼容性原因，Active Directory 域使用它来处理来自为早期基于 Windows 的客户端和服务器以及第三方操作系统设计的应用程序的网络身份验证请求。
    * 当在客户端和服务器之间使用 NTLM 协议时，服务器必须联系 DC 上的域身份验证服务，以验证客户端凭据。 服务器通过将客户端凭据转发到客户端帐户域中的 DC 来对客户端进行身份验证。
    * 当两个 Active Directory 域或林连接到一个信任时，可以路由使用这些协议发出的身份验证请求，以提供对这两个林中的资源的访问权限。

## <a name="authorization-and-access-control"></a>授权和访问控制

授权和信任技术共同提供跨 Active Directory 域或林的安全通信基础结构。 授权确定用户对域中的资源拥有哪一级别的访问权限。 信任通过提供用于对其他域中的用户进行身份验证的路径来促进用户的跨域授权，因此，对这些域中共享资源的请求可以获得授权。

当信任域中发出的身份验证请求通过受信任的域进行验证时，会将其传递到目标资源。 然后，目标资源将根据其访问控制配置来确定是否对受信任域中的用户、服务或计算机发出的特定请求授权。

信任提供此机制来验证传递到信任域的身份验证请求。 资源计算机上的访问控制机制确定授予受信任域中的请求者的最终访问级别。

## <a name="next-steps"></a>后续步骤

若要了解有关信任的详细信息，请参阅[林信任如何在 AZURE AD DS 中工作？][concepts-trust]

若要开始使用资源林创建 Azure AD DS 托管域，请参阅[创建和配置 AZURE AD ds 托管域][tutorial-create-advanced]。 然后，你可以[创建到本地域的出站林信任（预览）][create-forest-trust]。

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
