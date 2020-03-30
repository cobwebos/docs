---
title: Azure AD 域服务的资源林概念 |微软文档
description: 了解 Azure 活动目录域服务中的资源林是什么，以及它们如何在具有有限用户身份验证选项或安全问题的混合环境中使组织受益。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233605"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure 活动目录域服务的资源林概念和功能

Azure 活动目录域服务 （AD DS） 为旧版本地业务线应用程序提供登录体验。 本地和云用户的用户、组和密码哈希将同步到 Azure AD DS 托管域。 这些同步的密码哈希是为用户提供可用于本地 AD DS、Office 365 和 Azure 活动目录的单个凭据集的原因。

尽管安全并提供其他安全优势，但某些组织无法将这些用户密码哈希同步到 Azure AD 或 Azure AD DS。 组织中的用户可能不知道其密码，因为他们只使用智能卡身份验证。 这些限制阻止某些组织使用 Azure AD DS 提升本地经典应用程序并将其转移到 Azure。

为了满足这些需求和限制，您可以创建使用资源林的 Azure AD DS 托管域。 本文的概念文章解释了什么是林，以及它们如何信任其他资源来提供安全的身份验证方法。 Azure AD DS 资源林当前处于预览状态。

> [!IMPORTANT]
> Azure AD DS 资源林当前不支持 Azure HDInsight 或 Azure 文件。 默认的 Azure AD DS 用户林确实支持这两种附加服务。

## <a name="what-are-forests"></a>什么是森林？

*林*是活动目录域服务 （AD DS） 用于对一个或多个*域*进行分组的逻辑构造。 然后，域为用户或组存储对象，并提供身份验证服务。

在 Azure AD DS 中，林只包含一个域。 本地 AD DS 林通常包含许多域。 在大型组织中，尤其是在合并和收购之后，您最终可能会拥有多个本地林，每个林都包含多个域。

默认情况下，Azure AD DS 托管域将作为*用户*林创建。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 用户帐户可以直接针对 Azure AD DS 托管域进行身份验证，例如登录到加入域的 VM。 当用户林可以同步密码哈希，并且用户不使用专用登录方法（如智能卡身份验证）时，用户林将起作用。

在 Azure AD DS*资源*林中，用户通过本地 AD DS 的单向林*信任*进行身份验证。 使用此方法，用户对象和密码哈希不会同步到 Azure AD DS。 用户对象和凭据仅存在于本地 AD DS 中。 此方法允许企业在 Azure 中托管依赖于经典身份验证（如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，但删除了任何身份验证问题或疑虑。 Azure AD DS 资源林当前处于预览状态。

资源林还提供一次提升和移动应用程序一个组件的功能。 许多旧式本地应用程序是多层的，通常使用 Web 服务器或前端和许多与数据库相关的组件。 这些层使得很难一步提升整个应用程序并将其转移到云中。 使用资源林，可以分阶段将应用程序提升到云中，从而更轻松地将应用程序移动到 Azure。

## <a name="what-are-trusts"></a>什么是信任？

具有多个域的组织通常需要用户访问不同域中的共享资源。 访问这些共享资源需要一个域中的用户对另一个域进行身份验证。 要在不同域中的客户端和服务器之间提供这些身份验证和授权功能，必须在*两个域*之间建立信任。

使用域信任，每个域的身份验证机制信任来自另一个域的身份验证。 信任通过验证传入身份验证请求是否来自受信任的机构（*受信任的*域），帮助提供对资源域（*信任*域）中的共享资源的受控访问。 信任充当仅允许经过验证的身份验证请求在域之间移动的桥梁。

信任如何传递身份验证请求取决于其配置方式。 信任可以通过以下方式之一进行配置：

* **单向**- 提供从受信任域到信任域中的资源的访问权限。
* **双向**- 提供从每个域对其他域中的资源的访问。

信任也配置为以下列方式之一处理其他信任关系：

* **非传递 -** 信任仅存在于两个信任伙伴域之间。
* **传递 -** 信任会自动扩展到任一合作伙伴信任的任何其他域。

在某些情况下，在创建域时会自动建立信任关系。 其他时候，您必须选择信任类型并显式建立适当的关系。 所使用的信任的特定类型以及这些信任关系的结构取决于 Active Directory 目录服务的组织方式，以及不同版本的 Windows 在网络上是否共存。

## <a name="trusts-between-two-forests"></a>两个林之间的信任

您可以通过手动创建单向或双向林信任将单个林中的域信任扩展到另一个林。 林信任是仅存在于林根域和第二个林根域之间的传递信任。

* 单向林信任允许一个林中的所有用户信任另一个林中的所有域。
* 双向林信任在两个林中的每个域之间形成传递信任关系。

森林信托的过境性仅限于两个森林伙伴。 林信任不会扩展到任一合作伙伴信任的其他林。

![从 Azure AD DS 到本地 AD DS 的林信任图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

您可以根据组织的活动目录结构创建不同的域和林信任配置。 Azure AD DS 仅支持单向林信任。 在此配置中，Azure AD DS 中的资源可以信任本地林中的所有域。

## <a name="supporting-technology-for-trusts"></a>支持信托技术

信任使用各种服务和功能（如 DNS）在合作林中定位域控制器。 信任还依赖于 NTLM 和 Kerberos 身份验证协议以及基于 Windows 的授权和访问控制机制，以帮助跨 Active Directory 域和林提供安全的通信基础结构。 以下服务和功能有助于支持成功的信任关系。

### <a name="dns"></a>DNS

AD DS 需要 DNS 进行域控制器 （DC） 位置和命名。 DNS 提供以下支持，使 AD DS 正常工作：

* 允许网络主机和服务定位 DC 的名称解析服务。
* 使企业能够在其目录服务域的名称中反映其组织结构的命名结构。

通常部署一个 DNS 域名域域空间，该域域域空间反映了 AD DS 域名域。 如果在 AD DS 部署之前存在现有的 DNS 命名空间，则通常会为 Active Directory 分区 DNS 命名空间，并为 Active Directory 林根创建 DNS 子域和委派。 然后为每个活动目录子域添加其他 DNS 域名。

DNS 还用于支持活动目录 DC 的位置。 DNS 区域填充了 DNS 资源记录，这些记录使网络主机和服务能够定位活动目录 DC。

### <a name="applications-and-net-logon"></a>应用程序和净登录

应用程序和 Net 登录服务都是 Windows 分布式安全通道模型的组件。 与 Windows 服务器和 Active Directory 集成的应用程序使用身份验证协议与 Net Logon 服务通信，以便可以建立可以进行身份验证的安全路径。

### <a name="authentication-protocols"></a>身份验证协议

活动目录 DC 使用以下协议之一对用户和应用程序进行身份验证：

* **Kerberos 版本 5 身份验证协议**
    * Kerberos 版本 5 协议是运行 Windows 和支持第三方操作系统的本地计算机使用的默认身份验证协议。 该协议在 RFC 1510 中指定，并与 Active Directory、服务器消息块 （SMB）、HTTP 和远程过程调用 （RPC）以及使用这些协议的客户端和服务器应用程序完全集成。
    * 使用 Kerberos 协议时，服务器不必与 DC 联系。 相反，客户端通过从服务器帐户域中的 DC 请求服务器票证来获取服务器票证。 然后，服务器无需咨询任何其他机构即可验证票证。
    * 如果事务中涉及的任何计算机不支持 Kerberos 版本 5 协议，则使用 NTLM 协议。

* **NTLM 身份验证协议**
    * NTLM 协议是旧操作系统使用的经典网络身份验证协议。 出于兼容性原因，Active Directory 域使用它来处理来自早期基于 Windows 的客户端和服务器以及第三方操作系统的应用程序的网络身份验证请求。
    * 在客户端和服务器之间使用 NTLM 协议时，服务器必须联系 DC 上的域身份验证服务以验证客户端凭据。 服务器通过将客户端凭据转发到客户端帐户域中的 DC 来验证客户端。
    * 当两个活动目录域或林由信任连接时，可以使用这些协议进行的身份验证请求进行路由，以提供对两个林中的资源的访问。

## <a name="authorization-and-access-control"></a>授权和访问控制

授权和信任技术协同工作，跨 Active Directory 域或林提供安全的通信基础结构。 授权确定用户对域中的资源具有的访问级别。 信任通过提供一条用于验证其他域中用户的路径，以便授权用户对这些域中共享资源的请求，从而促进用户的跨域授权。

当受信任域中所做的身份验证请求由受信任的域验证时，它将传递给目标资源。 然后，目标资源根据访问控制配置确定是否授权受信任域中的用户、服务或计算机发出的特定请求。

信任提供此机制来验证传递到信任域的身份验证请求。 资源计算机上的访问控制机制确定授予受信任域中的请求者的最终访问级别。

## <a name="next-steps"></a>后续步骤

要了解有关信任的更多详细信息，请参阅[林信任如何在 Azure AD DS 中工作？][concepts-trust]

要开始使用资源林创建 Azure AD DS 托管域，请参阅[创建和配置 Azure AD DS 托管域][tutorial-create-advanced]。 然后，您可以[创建到本地域的出站林信任（预览）。][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
