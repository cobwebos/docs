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
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209410"
---
在本部分中，会在 Windows Server 上安装 Azure 文件同步代理。
[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)说明了需要关闭**IE 增强安全性**。 IE 增强安全性是一种安全措施，不适用于 Azure 文件同步并将其关闭，使你能够在 Azure 中进行身份验证，而不会出现任何问题。

打开 PowerShell 并安装以下命令所需的 PowerShell 模块。 请确保在出现提示时安装完整的模块和 NuGet 提供程序：

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果从服务器进入 internet 时遇到任何问题，现在就可以解决这些问题。 Azure 文件同步使用到 internet 的任何可用网络连接。
还支持要求代理服务器访问 internet。 你可以立即配置计算机范围的代理，也可以在代理安装过程中指定仅文件同步将使用的代理。

如果这意味着需要为此服务器打开防火墙，则这可能是可以接受的方法。 服务器安装结束后，在完成服务器注册后，将出现一个网络连接报告，其中显示了 Azure 中的确切终结点 Url，文件同步需要与所选区域的通信。 该报表还会告诉您需要进行通信的原因。 可以使用报表将此服务器上的防火墙锁定到特定的 Url。

你还可以遵循更保守的方法，在这种方法中，你不会在其中打开防火墙，而是将服务器限制为与更高级别的 DNS 名称空间进行通信- [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)一文中提供了更多文档和详细信息。 遵循自己的网络最佳实践。

在服务器*安装*向导结束时，将弹出一个服务器*注册*向导。
从以前的 Azure 资源将服务器注册到你的存储同步服务。

部署指南中更详细地介绍了这些步骤，包括应该首先安装的上述 PowerShell 模块： [Azure 文件同步代理安装](../articles/storage/files/storage-sync-files-deployment-guide.md)。

应使用最新的代理，可以从 Microsoft 下载中心下载： [Azure 文件同步代理](https://aka.ms/AFS/agent "下载 Azure 文件同步代理")。

成功安装和服务器注册后，你可以检查是否已成功完成此步骤：导航到 Azure 门户中的存储同步服务资源，然后单击左侧菜单中的 "已注册服务器"。 你会看到你的服务器立即列在此处。
