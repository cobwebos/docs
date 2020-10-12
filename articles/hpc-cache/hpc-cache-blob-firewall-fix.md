---
title: 解决存储防火墙设置
description: 在 Azure HPC 缓存中创建 Azure Blob 存储目标时，存储帐户网络防火墙设置可能会导致故障。 本文提供了对此限制的解决方法，直到软件修补准备就绪。
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: v-erkel
ms.openlocfilehash: 6916c79e9110a88beff65d487fac72441382c2f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092364"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解决 Blob 存储帐户防火墙设置问题

存储帐户防火墙中使用的特定设置会导致 Blob 存储目标创建失败。 为了解决此问题，Azure HPC 缓存团队正在处理软件修复，但你可以按照本文中的说明解决此问题。

仅允许从 "所选网络" 进行访问的防火墙设置可以阻止缓存创建 Blob 存储目标。 此配置位于存储帐户的 " **防火墙和虚拟网络** 设置" 页中。

问题在于缓存服务使用独立于客户环境的隐藏服务虚拟网络。 无法显式授权此网络访问你的存储帐户。

创建 Blob 存储目标时，缓存服务将使用此网络检查容器是否为空。 如果防火墙不允许从隐藏的网络进行访问，则检查将失败，并且存储目标创建会失败。

若要解决此问题，请在创建存储目标时暂时更改防火墙设置：

1. 转到 "存储帐户" " **防火墙和虚拟网络** " 页，并将 "允许访问" 设置更改为 " **所有网络**"。
1. 在 Azure HPC 缓存中创建 Blob 存储目标。
1. 成功创建存储目标后，将该帐户的防火墙设置改回 "选定的 **网络**"。

Azure HPC 缓存不使用服务虚拟网络来访问已完成的存储目标。

有关此解决方法的帮助，请 [与 Microsoft 服务和支持联系](hpc-cache-support-ticket.md)。
