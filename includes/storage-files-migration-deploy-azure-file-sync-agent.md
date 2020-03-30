---
title: 部署 Azure 文件同步代理
description: 部署 Azure 文件同步代理。 在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209410"
---
在本节中，您将在 Windows 服务器上安装 Azure 文件同步代理。
[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)说明您需要关闭**IE 增强的安全性**。 IE 增强的安全性是一种不适用于 Azure 文件同步的安全措施，关闭它允许您对 Azure 进行身份验证，而不会出现任何问题。

打开 PowerShell 并安装具有以下命令所需的 PowerShell 模块。 请务必在提示时安装完整模块和 NuGet 提供程序：

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果您有任何问题，从你的服务器到达互联网，现在是时候解决这些问题。 Azure 文件同步使用任何可用的互联网连接到互联网。
还支持要求代理服务器访问互联网。 您可以立即配置计算机范围的代理，也可以指定在代理安装期间仅使用文件同步的代理。

如果这意味着您需要为此服务器打开防火墙，那么这可能是您可以接受的方法。 在服务器安装结束时，在完成服务器注册后，将有一个网络连接报告，显示 Azure 中的确切终结点 URL，该文件同步需要与所选区域进行通信。 该报告还告诉您需要沟通的原因。 您可以使用报表将此服务器周围的防火墙锁定为特定的 URL。

您还可以遵循更保守的方法，即不打开防火墙范围，而是限制服务器以与更高级别的 DNS 名称空间进行通信 - [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)文章中提供了更多文档和详细信息。 遵循您自己的网络最佳实践。

在服务器*安装*向导的末尾，将弹出一个服务器*注册*向导。
从较早时间将服务器注册到存储同步服务 Azure 资源。

这些步骤在部署指南中进行了更详细的介绍，包括应首先安装的上述 PowerShell 模块：安装[Azure 文件同步代理](../articles/storage/files/storage-sync-files-deployment-guide.md)。

应使用最新的代理，并可以从 Microsoft 下载中心下载[：Azure 文件同步代理](https://aka.ms/AFS/agent "Azure 文件同步代理下载")。

成功安装和服务器注册后，可以检查您是否成功完成了此步骤：导航到 Azure 门户中的存储同步服务资源，然后按照左侧菜单导航到"已注册服务器"。 您将看到您的服务器列出在那里马上。
