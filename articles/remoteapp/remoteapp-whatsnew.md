---
title: "Azure RemoteApp 中有哪些新功能？ | Microsoft Docs"
description: "了解对 Azure RemoteApp 所做的更改和改进"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 40f1ba79-80f1-47bd-bf39-f86c03e2306a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 05487c4bbdce24ea9f31ed451026531c16ead7c4


---
# <a name="whats-new-in-azure-remoteapp"></a>Azure RemoteApp 中有哪些新功能？
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 的其中一个优势是我们一直在努力改进它。 每次做出改进之后，我们都会在这里公布。

## <a name="future-updates"></a>未来的更新
你是否知道 Azure RemoteApp 团队每月都会将更新张贴到 RDS 博客上？ 在这个博客上，不仅可以找到对 Azure RemoteApp 所做的更改，而且可以找到其他关于如何使用 RDS 的信息。 有关信息请查看他们的博客，[远程桌面服务博客](https://blogs.msdn.microsoft.com/rds/)。 例如，就在几个星期前，他们张贴了一个关于[使用 Azure RemoteApp 和 Azure AD 搬运工作负荷](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/)的条目。

## <a name="september-2015"></a>2015 年 9 月
* 将 Infopath 添加到了 Microsoft Office 365 模板和库映像中。 如果你想共享 Infopath，请确保使用最新的映像更新你的集合。
* 客户端更新：
  * Windows 客户端进行了更新，以使用户能够共享反馈，尤其是关于连接问题的反馈。
  * iOS 客户端进行了更新，修复了消息处理错误，并修复了凭据早于预期过期的问题。
* 我们正在致力于对 Office 2016 支持进行测试。 完成测试之后，请查找更新后的映像。
* 发布了一篇关于[云集合与混合集合之间的差异](remoteapp-collections.md)的新文章 - 这篇文章将帮助你选择最适合你应用的集合类型 - 仅云、云 + VNET 或混合。
* 是否想要使用 Azure RemoteApp 共享 QuickBooks，但不知道该如何操作？ 查看 [Eric 的新文章](remoteapp-quickbooks.md)，这篇文章会告诉你究竟应该如何操作。

## <a name="august-2015"></a>2015 年 8 月
8 月有很大的更改 - 以下是要点︰

* 你现在可以将 Azure VNET 与云集合一起使用了！ 查看[云创建说明](remoteapp-create-cloud-deployment.md)了解新的步骤。
* 对于 Windows RemoteApp 客户端，使得将应用添加到“**开始**”菜单成为可能。 应用将显示在应用程序列表中，并且你可以将它们固定到 Windows 的“**开始**”菜单中。
* 将一个新的映像添加到了 Azure VM 库中 - Windows Server 远程桌面会话主机与 Microsoft Office 365 ProPlus。
* 修复了 Mac 客户端，使得具有模式窗口的应用将停止冻结。
* 记录了你如何可以将 [Office 365 ProPlus 订阅](remoteapp-officesubscription.md)与 Azure RemoteApp 一起使用。
* 详细说明了如何可以在 Azure RemoteApp 集合中[保护应用和数据的安全](remoteapp-secure.md)。

## <a name="july-2015"></a>2015 年 7 月
7 月设置了将在 8 月进行的更改的阶段，现在并没有多少要讨论的内容，大部分都是文档更新。 以下是最近的更改︰

* 在门户上添加了“**支持**”选项卡，这样可以更容易地访问支持资源，比如论坛。
* 重做了疑难解答信息以用于创建一个混合集合。 查看[最新最好的资源](remoteapp-hybridtrouble.md)获得有关疑难解答的提示，比如如何识别要为 VNET 配置的正确端口。
* 记录了如何在 Azure RemoteApp 中创建和保存[用户数据](remoteapp-upd.md)。
* 记录了如何[锁定应用](remoteapp-secure.md)。
* 发布了 [Azure RemoteApp cmdlet](https://msdn.microsoft.com/library/mt428031.aspx)。
* 最后，我们与一些 Azure RemoteApp 用户进行了对话，讨论了关于术语的话题。 查找针对我们谈及不同集合选项的方式进行的更改。

## <a name="june-2015"></a>2015 年 6 月
有很多更改！ 团队在 6 月一直很忙︰

* 重新设计了 Azure RemoteApp [登陆页面](https://www.remoteapp.windowsazure.com/) - 试试看！
* 在作为你的订阅的一部分提供的所有映像中更新了软件。
* 对混合集合进行了改进，包括强制隧道支持以及在尝试创建集合之前检查 IP 子网大小。
* 发现了 * 通配符并不适用于网络摄像机。 相反，你需要指定实例 ID 或 GUID。 我们将更新重定向信息以反映这一点。
* 这样，在你从 Azure 库创建模板映像时，可以将自定义防病毒软件添加到映像中。

我们会在 7 月推出更多更改，因此很快就会有另一次更新。

## <a name="may-2015"></a>2015 年 5 月
自从我们第一次创建此主题以来，增加了许多内容（和月份），因此，此列表会忽略一些内容，它包括 3 月初至 5 月的内容。 查看这些新功能︰

* 让所有操作自动进行 - 现在 Azure RemoteApp [在 Azure PowerShell 模块中有 cmdlet](remoteapp-tutorial-arawithpowershell.md)。
* [从 Azure 虚拟机创建 Azure RemoteApp 映像](remoteapp-image-on-azurevm.md)。 让自定义映像上载到 Azure 的速度更快。
* 使用 Azure VNET（而不是 RemoteApp VNET）将你的企业网络资源连接到 Azure。 我们已更新 [混合集合说明](remoteapp-create-hybrid-deployment.md)来指导你完成 Azure VNET 的创建（这是第 1 步）。
* 谈到 VNET，请查看关于 VNET 大小限制和局限性的[新的指导原则](remoteapp-vnetsizing.md)。
* 而谈到限制 - 就只是[服务限制和默认设置](../azure-subscription-service-limits.md)吗？

 想要详细了解 Azure RemoteApp？ RemoteApp 团队曾在几周前齐聚一堂，讨论 Ignite。 查看 Eric 的视频，[Microsoft Azure RemoteApp 管理与行政基础](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)。

需要查看现实世界中的 Azure RemoteApp 吗？ 查看[在任何地方的任何设备上运行任何应用](remoteapp-anyapp.md)教程 - 该教程演示了如何与你的用户共享访问权限，包括共享数据库文件。 我们还有一个教程，是关于[使 Office 365](remoteapp-tutorial-o365anywhere.md) 在任何设备上运行相同内容的。

感谢你对我们的关注 - 请期待下一个月的更多更新。

### <a name="help-us-help-you"></a>请帮助我们改进，以便为你提供更好的帮助和支持
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”  进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。




<!--HONumber=Dec16_HO2-->


