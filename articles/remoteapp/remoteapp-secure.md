---
title: "保护 Azure RemoteApp 中的应用和资源的安全 |Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中锁定应用和资源"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7fbade87-a453-426d-bfa5-c72227ea83cd
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 13085f51529dadb739b4c629bb50d8aff0c9d8c2
ms.lasthandoff: 03/31/2017


---
# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>保护 Azure RemoteApp 中的应用和资源的安全
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 为用户提供对集中管理的 Windows 应用的访问，从而让你能够控制你的用户可以做什么和不能做什么。  当用户正在从非托管设备（比如其个人 Macbook）进行连接，并且你希望控制用户的访问权限或体验时，这特别有用。

例如，如果你正在使用 Active Directory 进行用户身份验证，并且希望禁止用户将数据复制到应用之外，那么可以配置一个远程桌面组策略来阻止用户复制数据。

又例如，如果你希望阻止你的集合中的某个特定应用进行互联网访问。 你可以在为你的集合创建映像时创建一条阻止访问的 Windows 防火墙规则。

## <a name="implementation-options"></a>实现选项
  下面是一些关键实现选项，可以根据需要单独使用或串联使用：

1. 如果你的 RemoteApp 集合已加入域，那么你可以强制实施任何[组策略](https://technet.microsoft.com/library/cc725828.aspx)（[此处](../azure-subscription-service-limits.md)描述的空闲和断开连接超时策略除外）。
2. 作为组策略（如果你的集合未加入域或者你在 AD 中没有适当的权限）的一种替代方式，你可以将[本地策略](https://technet.microsoft.com/library/cc775702.aspx)配置到你的模板映像中。  请注意，当存在冲突时，组策略的优先级比本地策略高。
3. 在配置模板映像时，某些操作系统/应用设置无法通过策略进行配置，但可以使用 [RegEdit 工具](remoteapp-hybridtrouble.md)通过注册表项进行配置。
4. 你可以使用 [Windows 防火墙](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish)来控制对运行应用所在计算机的网络访问，以及控制从运行应用所在的计算机进行网络访问。 只需确保不要阻止此处定义的 URL 和端口即可。
5. 你可以使用 [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) 来控制用户可以运行哪些应用程序和文件。 例如，精通技术的用户可以弄清楚如何运行你未发布但在你用于创建集合的映像中可以找到的应用程序 - AppLocker 可以阻止此操作。

## <a name="detailed-information"></a>详细信息
* 以下 RDS 策略很可能是最有用的︰
  * [设备和资源重定向](https://technet.microsoft.com/library/ee791794.aspx)
  * [打印机重定向](https://technet.microsoft.com/library/ee791784.aspx)
  * [配置文件](https://technet.microsoft.com/library/ee791865.aspx)。
* 请注意，通过 RemoteApp PowerShell 模块配置重定向（如[此处](remoteapp-redirection.md)所示）依赖客户端计算机强制实施该策略，因此，如果安全性是主要目标，你会希望通过模板映像本地策略或通过组策略强制实施该策略。
* [Windows Server 2012 R2 策略](https://technet.microsoft.com/library/hh831791.aspx)。
* [Office 2013 策略](https://technet.microsoft.com/library/cc178969.aspx)（包括[如何自定义 Office 工具栏](https://technet.microsoft.com/library/cc179143.aspx)）。


