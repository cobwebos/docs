---
title: "Azure Active Directory Connect Health 常见问题 - Azure | Microsoft 文档"
description: "此常见问题回答了关于 Azure AD Connect Health 的问题。 其中涵盖了服务使用方面的问题，包括计费模式、功能、限制和支持。"
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect Health 常见问题 (FAQ)
此常见问题回答了关于 Azure AD Connect Health 的问题。 其中涵盖了服务使用方面的问题，包括计费模式、功能、限制和支持。

## <a name="general-questions"></a>一般问题
**问：我要管理多个 Azure AD 目录。如何在 Azure 门户中的不同目录之间切换？**

若要切换 Azure AD 租户，可以在右上角选择当前登录的用户名，然后选择相应的帐户。 如果帐户未在此列出，请选择“注销”，然后使用 Directory 的适当凭据登录。

**问：Azure AD Connect Health 支持哪个版本的标识角色？**

下表列出了角色，以及支持的操作系统版本。

|角色| 操作系统 / 版本|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 版本 1.0.9125 或更高版本。|
|AD DS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

请注意，服务提供的功能可能因角色和操作系统的不同而不同。 换言之，并非所有功能都适用于所有 OS 版本。 有关详细信息，请参阅功能说明。

**问：需要多少许可证来监视我的基础结构？**

* 第一个 Connect Health 代理需要至少一个 Azure AD Premium 许可证。
* 每个额外的注册代理需要 25 个额外的 AADP 许可证。 
* 代理计数等于在所有受监视角色（AD FS、Azure AD Connect 和/或 AD DS）中注册的总代理数

许可信息还可在 [Azure AD 定价页](https://aka.ms/aadpricing)中找到。

示例：

| 注册的代理数 | 所需的许可证数 | 监视配置示例 |
| ------ | --------------- | --- |
| 1 | 1 | 1 个 AAD Connect 服务器 |
| 2 | 26| 1 个 AAD Connect 服务器和 1 个域控制器 |
| 3 | 51 | 1 个 AD FS 服务器、1 个 AD FS 代理和 1 个域控制器 |
| 4 | 76 | 1 个 AD FS 服务器、1 个 AD FS 代理和 2 个域控制器 |
| 5 | 101 | 1 个 AAD Connect 服务器、1 个 AD FS 服务器、1 个 AD FS 代理和 2 个域控制器 |
## <a name="installation-questions"></a>安装问题
**问：在个别服务器上安装 Azure AD Connect Health 代理有何影响？**

安装 Microsoft Azure AD Connect Health 代理 ADFS、Web 应用程序代理服务器、Azure AD Connect（同步）服务器、域控制器对 CPU、内存消耗、网络带宽和存储的影响很小。

下面的数字为近似值。

    * CPU 占用率：增加约&1;-5%
    * 内存消耗：最多为系统总内存的 10%

> [!NOTE]
> 如果运行状况代理丢失与 Connect Health 服务的连接，它将在本地存储数据，直至达到所定义的上限。 代理会根据“最近获得的服务最少”这一标准覆盖“缓存的”数据。
>
>

* Azure AD Connect Health 代理的本地缓冲区存储：约&20; MB
* 对于 AD FS 服务器，建议预配一个 1024 MB (1 GB) 大小的磁盘空间，方便 Azure AD Connect Health 代理的 AD FS 审核通道在所有审核数据被覆盖之前将其处理掉。

**问：在 Azure AD Connect Health 代理安装期间，我必须重新启动我的服务器吗？**

否。 安装代理时不需要重新启动服务器。

安装某些必备组件时可能需要重新启动服务器。 例如，在 Windows Server 2008 R2 上安装 .Net 4.5 Framework 需要重启服务器。

**问：Azure AD Connect Health Services 是否通过直通型 http 代理进行工作？**

是的。  对于正在进行的操作，你可以将 Health 代理配置为使用 HTTP 代理转发出站 http 请求。 有关详细信息，请阅读[为 Health 代理配置 HTTP 代理](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

如果要在代理注册过程中配置代理，可能需要事先修改 Internet Explorer 代理设置。

1. 打开“Internet Explorer -> 设置 -> Internet 选项 -> 连接 -> LAN 设置”。
2. 选择“为 LAN 使用代理服务器”。
3. 如果针对 HTTP 和 HTTPS/Secure 设置不同的代理端口，请选择“高级”。

**问：Azure AD Connect Health Services 在连接到 Http 代理时是否支持基本身份验证？**

否。 目前不支持指定任意用户名/密码进行基本身份验证的机制。

**问：若要确保 Azure AD Connect Health 代理正常使用，需要打开哪些防火墙端口？**

有关防火墙端口列表和其他连接要求的信息，请参阅[要求部分](active-directory-aadconnect-health-agent-install.md#requirements)。


**问：Azure AD Connect Health 门户中为何有两个同名的服务器？**

从某个服务器中删除代理时，该服务器不会自动从 Azure AD Connect 门户中删除。  如果手动从服务器中删除代理或删除服务器本身，需要从 Azure AD Connect Health 门户中手动删除该服务器条目。

如果重建了服务器的映像或者创建了具有相同详细信息（如计算机名称）的新服务器，但没有从 Azure AD Connect Health 门户中删除已注册服务器，而是在新服务器上安装了代理，则会看到两个服务器条目具有同一名称。  
在这种情况下，你应手动删除属于原有服务器的条目。 此服务器的数据会过期。

## <a name="health-agent-registration-and-data-freshness"></a>Health 代理注册和数据刷新

**问：Health 代理注册故障的常见原因有哪些，如何排除故障？**

由于下列可能的原因，运行状况代理可能无法注册
    * 由于防火墙阻塞流量，而导致代理无法与所需的终结点通信。 这在 Web 应用程序代理服务器上尤其常见。 请确保你已允许出站通信到所需终结点和端口。 有关详细信息，请参阅[要求部分](active-directory-aadconnect-health-agent-install.md#requirements)。
    * 网络层会对出站通信进行 SSL 检查。 这会导致代理使用的证书被检查服务器/实体替换，并且无法执行完成代理注册所需的步骤。
    * 用户没有执行代理注册的访问权限。 默认情况下，全局管理员具有访问权限。 可使用[基于角色的访问控制](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)将访问权限委派给其他用户。

**问：我收到有关“Health 服务数据不是最新”的警报。如何排查问题？**

若最近 2 小时内未从服务器接收到所有数据点，Health 服务将生成此警报。 触发此警报的原因有多种。
    * 由于防火墙阻塞流量，而导致代理无法与所需的终结点通信。 这在 Web 应用程序代理服务器上尤其常见。 请确保你已允许出站通信到所需终结点和端口。 有关详细信息，请参阅[要求部分](active-directory-aadconnect-health-agent-install.md#requirements)。
    * 网络层会对出站通信进行 SSL 检查。 这会导致代理使用的证书被检查服务器/实体替换，并且无法将数据上传到 Connect Health 服务。
    * 可以使用代理中内置的连接命令。 [了解详细信息](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)。
    * 代理还支持通过未经身份验证的 HTTP 代理进行出站连接。 [了解详细信息](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy)。


## <a name="operations-questions"></a>操作问题
**问：我需要在 Web 应用程序代理服务器上启用审核吗？**

否，不需在 Web 应用程序代理 (WAP) 服务器上启用审核。

**问：如何解除 Azure AD Connect Health 警报？**

在成功的情况下，将解除 Azure AD Connect Health 警报。 Azure AD Connect Health 代理将定期检测并向服务报告成功的情况。 某些警报的解除取决于时间。 换句话说，如果在警报生成后的 72 小时内未观察到相同的错误条件，则警报会自动解除。


## <a name="migration-questions"></a>迁移问题

本部分仅适用于已收到通知的客户（通知内容为客户的 Azure AD Connect Health 服务数据即将被迁移）。

**问：在迁移后，是否需要重新注册代理，或重新配置通知设置？**

否，作为迁移的一部分，代理注册信息和通知设置将被一同移动。

**问：迁移开始后，要多久才能在门户中看到数据？**

在迁移后的一小时之内，数据将出现在门户中。

**问：现有的活动警报将如何？**

在迁移后的一小时之内，任何适用的警报都将被重新激活。


## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


