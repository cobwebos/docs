---
title: 部署 Azure 文件同步代理
description: 部署 Azure 文件同步代理。 跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143634"
---
在本部分中，会在 Windows Server 实例上安装 Azure 文件同步代理。

[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)说明了需要关闭**Internet Explorer 增强的安全配置**。 此安全措施不适用于 Azure 文件同步。关闭后，可以在 Azure 中进行身份验证，而不会出现任何问题。

打开 PowerShell，然后使用以下命令安装所需的 PowerShell 模块。 请确保在出现提示时安装完整的模块和 NuGet 提供程序。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果从服务器进入 internet 时遇到任何问题，现在就可以解决这些问题。 Azure 文件同步使用到 internet 的任何可用网络连接。 还支持要求代理服务器访问 internet。 你可以立即配置计算机范围的代理，也可以指定在代理安装过程中 Azure 文件同步将使用的代理。

如果配置代理，则表示需要为此服务器打开防火墙，这可能是一种可接受的方法。 在服务器安装结束时，在完成服务器注册后，网络连接报告将显示 Azure 中的确切终结点 Url，Azure 文件同步需要与所选区域的通信。 该报表还会告诉您需要进行通信的原因。 可以使用报表将此服务器上的防火墙锁定到特定的 Url。

你还可以遵循更保守的方法，在这种情况下，你不会在其中打开防火墙，而是限制服务器与更高级别 DNS 命名空间进行通信。 有关详细信息，请参阅 [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)。 遵循自己的网络最佳实践。

在服务器 *安装* 向导结束时，将打开一个服务器 *注册* 向导。 从前面的存储同步服务的 Azure 资源注册服务器。

部署指南中更详细地介绍了这些步骤，包括应该首先安装的 PowerShell 模块： [Azure 文件同步代理安装](../articles/storage/files/storage-sync-files-deployment-guide.md)。

使用最新的代理。 你可以从 Microsoft 下载中心下载： [Azure 文件同步代理](https://aka.ms/AFS/agent "下载 Azure 文件同步代理")。

成功安装和服务器注册后，可以检查是否已成功完成此步骤。 中转到 Azure 门户中的存储同步服务资源，然后单击左侧菜单中的 " **已注册的服务器**"。 你将看到你的服务器已在此处列出。
