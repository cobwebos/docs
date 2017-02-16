---
title: "Azure AD Connect Health 常见问题"
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
ms.date: 12/16/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 2f990f4c888da91422a6a6e9873307cbb483e782
ms.openlocfilehash: b6687bc0ccde0e257338a939f39e343f75a6f5df


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect Health 常见问题 (FAQ)
此常见问题回答了关于 Azure AD Connect Health 的问题。 其中涵盖了服务使用方面的问题，包括计费模式、功能、限制和支持。

## <a name="general-questions"></a>一般问题
**问：我要管理多个 Azure AD 目录。如何切换到使用 Azure Active Directory Premium 的租户？**

若要切换 Azure AD 租户，可以在右上角选择当前登录的用户名，然后选择相应的帐户。 如果此处未列出该帐户，你可选择“注销”，然后使用已启用 Azure Active Directory Premium 的目录的全局管理员凭据来登录。

## <a name="installation-questions"></a>安装问题
**问：在个别服务器上安装 Azure AD Connect Health 代理有何影响？**

安装 Microsoft Azure AD Connect Health 代理 ADFS、Web 应用程序代理服务器、Azure AD Connect (同步)服务器、域控制器对 CPU、内存消耗、网络带宽和存储的影响很小。

下面的数字为近似值。

* CPU 占用率：增加约 1%
* 内存消耗：最多为系统总内存的 10%

> [!NOTE]
> 如果代理无法与 Azure 通信，则代理会按照定义的上限将数据存储在本地。 代理会根据“最近获得的服务最少”这一标准覆盖“缓存的”数据。
> 
> 

* Azure AD Connect Health 代理的本地缓冲区存储：约 20 MB
* 对于 AD FS 服务器，建议预配一个 1024 MB (1 GB) 大小的磁盘空间，方便 Azure AD Connect Health 代理的 AD FS 审核通道在所有审核数据被覆盖之前将其处理掉。

**问：在 Azure AD Connect Health 代理安装期间，我必须重新启动我的服务器吗？**

否。 安装代理时不需要重新启动服务器。 但是，安装某些必备组件时可能需要重新启动服务器。

例如，在 Windows Server 2008 R2 上安装 .Net 4.5 Framework 需要重启服务器。

**问：Azure AD Connect Health Services 是否通过直通型 http 代理进行工作？**

是的。  对于正在进行的操作，你可以将 Health 代理配置为使用 HTTP 代理转发出站 http 请求。

如果要在代理注册过程中配置代理，可能需要事先修改 Internet Explorer 代理设置。

1. 打开“Internet Explorer -> 设置 -> Internet 选项 -> 连接 -> LAN 设置”。
2. 选择“为 LAN 使用代理服务器”。
3. 如果针对 HTTP 和 HTTPS/Secure 设置不同的代理端口，请选择“高级”。

**问：Azure AD Connect Health Services 在连接到 Http 代理时是否支持基本身份验证？**

否。 目前不支持指定任意用户名/密码进行基本身份验证的机制。

**问：用于 AD DS 的 Azure AD Connect Health 支持哪个版本的 AD DS？**

在以下 OS 版本上安装后支持 AD DS 监视：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>操作问题
**问：我是否需要对 AD FS 应用程序代理服务器或 Web 应用程序代理服务器启用审核？**

否，不需在 Web 应用程序代理 (WAP) 服务器上启用审核。 可在 AD FS 服务器上启用它。

**问：如何解除 Azure AD Connect Health 警报？**

在成功的情况下，将解除 Azure AD Connect Health 警报。 Azure AD Connect Health 代理将定期检测并向服务报告成功的情况。 某些警报的解除取决于时间。 换句话说，如果在警报生成后的 72 小时内未观察到相同的错误条件，则警报会自动解除。

**问：若要确保 Azure AD Connect Health 代理正常使用，需要打开哪些防火墙端口？**

需要打开 TCP/UDP 端口 443 和 5671，才能让 Azure AD Connect Health 代理与 Azure AD Health 服务终结点进行通信。

**问：Azure AD Connect Health 门户中为何有两个同名的服务器？**

从某个服务器中删除代理时，该服务器不会自动从 Azure AD Connect 门户中删除。  如果手动从服务器中删除代理或删除服务器本身，需要从 Azure AD Connect Health 门户中手动删除该服务器条目。 

如果重建了服务器的映像或者创建了具有相同详细信息（如计算机名称）的新服务器，但没有从 Azure AD Connect Health 门户中删除已注册服务器，而是在新服务器上安装了代理，则会看到两个服务器条目具有同一名称。  
在这种情况下，你应手动删除属于原有服务器的条目。 此服务器的数据会过期。

## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


