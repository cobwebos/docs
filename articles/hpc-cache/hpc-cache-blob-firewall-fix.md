---
title: 围绕存储防火墙设置进行操作
description: 在 Azure HPC 缓存中创建 Azure Blob 存储目标时，存储帐户网络防火墙设置可能会导致失败。 本文给出了在软件修复到位之前限制的解决方法。
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174403"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解决 Blob 存储帐户防火墙设置问题

存储帐户防火墙中使用的特定设置可能会导致 Blob 存储目标创建失败。 Azure HPC 缓存团队正在处理此问题的软件修复程序，但您可以按照本文中的说明解决此问题。

仅允许从"选定网络"访问的防火墙设置可以阻止缓存创建 Blob 存储目标。 此配置位于存储帐户的**防火墙和虚拟网络**设置页中。

问题是缓存服务使用独立于客户环境的隐藏服务虚拟网络。 无法显式授权此网络访问您的存储帐户。

创建 Blob 存储目标时，缓存服务使用此网络检查容器是否为空。 如果防火墙不允许从隐藏网络进行访问，则检查失败，存储目标创建失败。

要解决此问题，请临时更改防火墙设置，同时创建存储目标：

1. 转到存储帐户**防火墙和虚拟网络**页面，并将"允许访问"设置更改为**所有网络**。
1. 在 Azure HPC 缓存中创建 Blob 存储目标。
1. 成功创建存储目标后，将帐户的防火墙设置更改回 **"选定网络**"。

Azure HPC 缓存不使用服务虚拟网络访问已完成的存储目标。

有关此解决方法的帮助，[请与 Microsoft 服务和支持 。](hpc-cache-support-ticket.md)
