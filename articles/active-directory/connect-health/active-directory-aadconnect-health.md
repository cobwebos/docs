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
ms.date: 10/18/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: fbb95696d8b9267d59d04769d3d7f48c29a8e4dd


---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>在云中监视本地标识基础结构和同步服务
Azure AD Connect Health 可帮助你监视和深入了解本地标识基础结构和同步服务。  它针对重要的标识组件 - 例如 AD FS 服务器、Azure AD Connect 服务器（即同步引擎）、Active Directory 域控制器等 - 提供监视功能，使你可以与 Office 365 和 Microsoft Online Services 建立可靠的连接。它还可以让用户轻松访问有关这些组件的关键数据点，获取使用情况信息和其他重要见解来做出明智的决策。

这些信息全部显示在 [Azure AD Connect Health 门户](https://aka.ms/aadconnecthealth)中。 使用 Azure AD Connect Health 门户可以查看警报、性能监视、使用情况分析等信息。 Azure AD Connect Health 在一个集中的位置提供重要标识组件的运行状况单一透视图。

![什么是 Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

将来对 Azure AD Connect Health 所做的更新将会包括附加的监视功能，以及深入分析其他标识组件的功能。 因此，它将通过标识透视图提供单个仪表板，让你能拥有更稳健的集成环境，使用户能够充分利用该环境来增强工作能力。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 但是，这种集成带来的挑战是，必须确保此环境正常运行，才能让用户从任何设备可靠地访问本地和云中的资源。 Azure AD Connect Health 提供简单的基于云的方法可让用户监视和深入分析用于访问 Office 365 或其他 Azure AD 应用程序的本地标识基础结构。 这种方法就像在每个本地标识服务器上安装代理那样简单。

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[适用于 AD FS 的 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
适用于 AD FS 的 Azure AD Connect Health 支持 Windows Server 2008 R2 中的 AD FS 2.0，以及 Windows Server 2012 和 Windows Server 2012R2 中的 AD FS。 它还支持监视为 Extranet 访问提供身份验证支持的 AD FS 代理或 Web 应用程序代理服务器。 适用于 AD FS 的 Azure AD Connect Health 的安装非常简单且费用低廉，同时提供以下重要功能集：

* 使用警报进行监视，以便在 AD FS 和 AD FS 代理服务器状况不正常时知道这一点
* 关键警报电子邮件通知
* 查看性能数据的趋势，这在规划 AD FS 的容量时非常有用
* 使用不同的透视图（应用、用户、网络位置等）了解有关 AD FS 登录的使用情况分析数据，这对于了解 AD FS 的使用情况非常有用。
* 针对 AD FS 的报告，例如，使用不当的用户名/密码尝试登录的前 50 个用户和使用的最后一个 IP 地址

以下视频将概述适用于 AD FS 的 Azure AD Connect Health

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
用于同步的 Azure AD Connect Health 可以监视和提供有关本地 Active Directory 与 Azure Active Directory 之间发生的同步的信息。 用于同步的 Azure AD Connect Health 提供以下关键功能集：

* 使用警报进行监视，以便在 Azure AD Connect 服务器（即同步引擎）状况不正常时知道这一点
* 关键警报电子邮件通知
* 包括延迟图表以不同操作和趋势保持同步的同步操作见解操作如窗体添加的更新、删除。
* 速览同步属性有关的信息、上次成功导出到 Azure AD
* 有关对象级同步错误的报告\(不需要 Azure AD Premium\)

以下视频概述了用于同步的 Azure AD Connect Health

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-ds-previewactive-directory-aadconnect-health-addsmd"></a>[用于 AD DS 的 Azure AD Connect Health（预览版）](active-directory-aadconnect-health-adds.md)
用于 AD DS 的 Azure AD Connect Health 负责监视 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 上已安装的域控制器。 通过安装简单、低成本的运行状况代理，可以直接从云中监视本地 AD DS 环境。 用于 AD DS 的 Azure AD Connect Health 提供以下关键功能集：

* 检测到域控制器不正常时的监视警报以及关键警报的电子邮件通知。
* 域控制器仪表板，提供对域控制器运行状况和运行状态的快速视图。
* 复制状态仪表板，显示最新复制信息以及指向故障排除指南的链接（检测到错误时）。
* 需要从任何位置快速访问常用性能计数器的性能数据关系图，才能实现故障排除和监视目的。

以下视频概述了用于 AD DS 的 Azure AD Connect Health

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health 入门
Azure AD Connect Health 很容易入门。 请遵循以下步骤进行配置：

1. [获取 Azure AD Premium](../active-directory-get-started-premium.md) 或[开始试用](https://azure.microsoft.com/trial/get-started-active-directory/)
2. [下载并安装 Azure AD Connect Health 代理](#download-and-install-azure-ad-connect-health-agent) 。
3. 在 [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)

> [!NOTE]
> 请记住在查看 Azure AD Connect Health 仪表板中的任何数据之前，你将需要在目标服务器上安装 Azure AD Connect Health 代理。
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>下载并安装 Azure AD Connect Health 代理
* 请务必满足 Azure AD Connect Health 的要求
* 若要开始使用用于 AD FS 的 Azure AD Connect Health，可在此处下载最新版本的代理：[下载用于 AD FS 的 Azure AD Connect Health 代理。](http://go.microsoft.com/fwlink/?LinkID=518973)
  [](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* 若要开始使用用于同步的 Azure AD Connect Health，请下载并安装 [最新版本的 Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771)。  在安装 Azure AD Connect 的过程中，将会安装 Health 代理（版本 1.0.9125.0 或更高）。  Azure AD Connect 支持从以前的版本就地升级。
* 若要开始使用用于 AD DS 的 Azure AD Connect Health，可在此处下载最新版本的代理：[下载用于 AD DS 的 Azure AD Connect Health 代理。](http://go.microsoft.com/fwlink/?LinkID=820540)
  [](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health 门户
可以使用 Azure AD Connect Health 门户查看警报、性能监视情况和使用情况分析。 单击 https://aka.ms/aadconnecthealth 可转到 Azure AD Connect Health 的主边栏选项卡。  你可以将边栏选项卡视为窗口。 在主边栏选项卡上，你将看到“快速启动”、“Azure AD Connect Health 中的服务”和其他配置选项。 在屏幕快照下方是上述每个选项卡的简短说明。  部署代理后，运行状况服务将自动标识 Azure AD Connect Health 正在监视的服务。

![Azure AD Connect Health 门户](./media/active-directory-aadconnect-health/portal4.png)

* **快速启动** – 选择此项将打开“快速启动”边栏选项卡。 在这里，你可以通过选择“获取工具”来下载 Azure AD Connect Health 代理，可以访问文档，还可以提供反馈。
* **Active Directory 联合身份验证服务** – 此项表示 Azure AD Connect Health 当前监视的所有 AD FS 服务。 选择其中一个实例后，就会打开一个边栏选项卡，其中包含该服务实例的相关信息。  该信息包括概述、属性、警报、监视情况，以及使用情况分析。 在 [此处](active-directory-aadconnect-health-adfs.md)
* **Azure Active Directory Connect（同步）** – 此项表示 Azure AD Connect Health 当前正在监视的 Azure AD Connect 服务器。 如果选择该项，边栏选项卡将会打开，其中显示了有关 Azure AD Connect 服务器的信息。 在 [此处](active-directory-aadconnect-health-sync.md)
* **Active Directory 域服务** – 此项表示 Azure AD Connect Health 当前监视的所有 AD DS 林。 选择其中一个林后，就会打开一个边栏选项卡，其中包含该林的相关信息。  该信息包括基本信息、域控制器仪表板、复制状态仪表板、警报和监视的概述。 在 [此处](active-directory-aadconnect-health-adds.md)
* **配置** – 使用此项可打开或关闭以下功能：

  1. 通过自动更新自动将 Azure AD Connect Health 代理更新到最新版本 - 这意味着，你将在 Azure AD Connect Health 代理发布时自动更新到最新版本的该软件。 此项已默认启用。
  2. 允许 Microsoft 访问你 Azure AD 目录的运行状况数据，但只能将其用于故障诊断目的 - 这意味着，如果启用此功能，Microsoft 将能够查看你所看到的数据。 这有助于进行故障诊断，并帮助你解决问题。 此项已默认禁用。

## <a name="related-links"></a>相关链接
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常见问题](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Dec16_HO3-->


