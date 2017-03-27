---
title: "在云中监视本地标识基础结构"
description: "本页介绍 Azure AD Connect Health 是什么，以及为何要使用它。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: d995918ef51f345bf76a25f9b6908cf5f4aac6c9
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>在云中监视本地标识基础结构和同步服务
Azure Active Directory (Azure AD) Connect Health 可帮助你监视和深入了解本地标识基础结构和同步服务。 它针对重要的标识组件 - 例如 Active Directory 联合身份验证服务 (AD FS) 服务器、Azure AD Connect 服务器（也称为同步引擎）、Active Directory 域控制器等 - 提供监视功能，以便与 Office 365 和 Microsoft Online Services 建立可靠的连接。它还可以让用户轻松访问有关这些组件的关键数据点，获取使用情况信息和其他重要见解来做出明智的决策。

这些信息显示在 [Azure AD Connect Health 门户](https://aka.ms/aadconnecthealth)中。 在 Azure AD Connect Health 门户中，可以查看警报、性能监视、使用情况分析和其他信息。 Azure AD Connect Health 在一个集中的位置提供重要标识组件的运行状况单一可重用功能区。

![什么是 Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

随着 Azure AD Connect Health 中的功能日益增多，门户通过标识可重用功能区提供单个仪表板。 你可以创建一个更稳健、更健全的集成环境，让用户提高其工作效率。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health？
将本地目录与 Azure AD 集成可以提高用户的工作效率，因为他们可以使用一个通用标识来访问云和本地资源。 但是，这种集成带来的挑战是，必须确保此环境正常运行，才能让用户从任何设备可靠地访问本地和云中的资源。 Azure AD Connect Health 可帮助你监视和深入分析用于访问 Office 365 或其他 Azure AD 应用程序的本地标识基础结构。 这种方法就像在每个本地标识服务器上安装代理那样简单。

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[适用于 AD FS 的 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
适用于 AD FS 的 Azure AD Connect Health 支持 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 中的 AD FS 2.0。 它还支持监视为 Extranet 访问提供身份验证支持的 AD FS 代理或 Web 应用程序代理服务器。 轻松安装费用低廉的 Health 代理后，适用于 AD FS 的 Azure AD Connect Health 可提供以下重要功能集：

* 使用警报进行监视，以便在 AD FS 和 AD FS 代理服务器状况不正常时知道这一点
* 关键警报电子邮件通知
* 查看性能数据的趋势，这在规划 AD FS 的容量时非常有用
* 使用透视图（应用、用户、网络位置等）了解有关 AD FS 登录的使用情况分析数据，这对于了解 AD FS 的使用情况非常有用
* 针对 AD FS 的报告，例如，使用不当的用户名/密码尝试登录的前 50 个用户和使用的最后一个 IP 地址

以下视频概述了适用于 AD FS 的 Azure AD Connect Health。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
用于同步的 Azure AD Connect Health 可以监视和提供有关本地 Active Directory 与 Azure AD 之间发生的同步的信息。 用于同步的 Azure AD Connect Health 提供以下关键功能集：

* 使用警报进行监视，以便在 Azure AD Connect 服务器（也称为同步引擎）状况不正常时知道这一点
* 关键警报电子邮件通知
* 包括延迟图表以不同操作和趋势保持同步的同步操作见解操作如窗体添加的更新、删除
* 速览有关同步属性以及上次成功导出到 Azure AD 的信息
* 有关对象级同步错误的报告\(不需要 Azure AD Premium\)

以下视频概述了用于同步的 Azure AD Connect Health。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[用于 AD DS 的 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
用于 Active Directory 域服务 (AD DS) 的 Azure AD Connect Health 负责监视 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上已安装的域控制器。 使用 Health 代理安装可以从云监视本地 AD DS 环境。 用于 AD DS 的 Azure AD Connect Health 提供以下关键功能集：

* 检测到域控制器不正常时的监视警报以及关键警报的电子邮件通知
* 域控制器仪表板，提供对域控制器运行状况和运行状态的快速视图
* 复制状态仪表板，显示最新复制信息以及指向故障排除指南的链接（检测到错误时）
* 需要从任何位置快速访问常用性能计数器的性能数据关系图，才能实现故障排除和监视目的

以下视频概述了用于 AD DS 的 Azure AD Connect Health。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health 入门
若要开始使用 Azure AD Connect Health，请执行以下步骤：

1. [获取 Azure AD Premium](../active-directory-get-started-premium.md) 或[开始试用](https://azure.microsoft.com/trial/get-started-active-directory/)。
2. 在标识服务器上[下载并安装 Azure AD Connect Health 代理](#download-and-install-azure-ad-connect-health-agent)。
3. 在 [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) 上查看 Azure AD Connect Health 仪表板。

> [!NOTE]
> 请记住，在查看 Azure AD Connect Health 仪表板中的数据之前，需要在目标服务器上安装 Azure AD Connect Health 代理。
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>下载并安装 Azure AD Connect Health 代理
* 请务必[满足 Azure AD Connect Health 的要求](active-directory-aadconnect-health-agent-install.md#requirements)。
* 适用于 AD FS 的 Azure AD Connect Health 使用入门
    * [下载适用于 AD FS 的 Azure AD Connect Health 代理。](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [查看安装说明](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* 适用于同步的 Azure AD Connect Health 使用入门
    * [下载并安装最新版 Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771)。 在安装 Azure AD Connect 的过程中，将会安装适用于同步的 Health 代理（1.0.9125.0 或更高版本）。
* 适用于 AD DS 的 Azure AD Connect Health 使用入门
    * [下载适用于 AD DS 的 Azure AD Connect Health 代理](http://go.microsoft.com/fwlink/?LinkID=820540)。
    * [查看安装说明](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)。

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health 门户
Azure AD Connect Health 门户显示警报、性能监视和使用情况分析的视图。 单击 https://aka.ms/aadconnecthealth URL 可转到 Azure AD Connect Health 的主边栏选项卡。 你可以将边栏选项卡视为窗口。 在主边栏选项卡上，可以看到“快速启动”、Azure AD Connect Health 中的服务和其他配置选项。 请参阅下面的屏幕截图及其后面的简要说明。 部署代理后，运行状况服务将自动标识 Azure AD Connect Health 正在监视的服务。

> [!NOTE]
> 有关许可信息，请参阅 [Azure AD Connect 常见问题](active-directory-aadconnect-health-faq.md)或 [Azure AD 定价页](https://aka.ms/aadpricing)。
    
![Azure AD Connect Health 门户](./media/active-directory-aadconnect-health/portal4.png)

* **快速启动**：选择此选项会打开“快速启动”边栏选项卡。 可以选择“获取工具”来下载 Azure AD Connect Health 代理。 还可以访问文档并提供反馈。
* **Active Directory 联合身份验证服务**：此选项显示 Azure AD Connect Health 当前正在监视的所有 AD FS 服务。 选择某个实例时，打开的边栏选项卡会显示有关该服务实例的信息。 该信息包括概述、属性、警报、监视情况，以及使用情况分析。 通过[在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md) 详细了解相关功能。
* **Azure Active Directory Connect (同步)**：此选项显示 Azure AD Connect Health 当前正在监视的 Azure AD Connect 服务器。 选择该项时，打开的边栏选项卡会显示有关 Azure AD Connect 服务器的信息。 通过[使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md) 详细了解相关功能。
* **Active Directory 域服务**：此选项显示 Azure AD Connect Health 当前正在监视的所有 AD DS 林。 选择某个林时，打开的边栏选项卡会显示有关该林的信息。 这些信息包括基本信息、域控制器仪表板、复制状态仪表板、警报和监视的概述。 通过[在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md) 详细了解相关功能。
* **配置**：此部分包含用于打开或关闭以下功能的选项：

  - 通过自动更新自动将 Azure AD Connect Health 代理更新到最新版本：在 Azure AD Connect Health 代理发布时自动更新到最新版本的该软件。 此项已默认启用。
  - 允许 Microsoft 访问你的 Azure AD 目录的运行状况数据，但只能将其用于故障排除目的：如果启用此功能，Microsoft 可以查看你所看到的数据。 这些信息有助于故障排除，并帮助解决问题。 此项已默认禁用。

## <a name="related-links"></a>相关链接
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常见问题](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)

