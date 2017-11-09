---
title: "使用 Azure 门户创建虚拟机规模集 | Microsoft Docs"
description: "使用 Azure 门户部署规模集。"
keywords: "虚拟机规模集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>如何使用 Azure 门户创建虚拟机规模集
本教程介绍如何通过 Azure 门户在数分钟内轻松创建虚拟机规模集。 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="choose-the-vm-image-from-the-marketplace"></a>从应用商店中选择 VM 映像
在门户中，你可以使用 CentOS、CoreOS、Debian、Ubuntu 服务器、其他 Linux 映像以及 Windows Server 映像轻松部署规模集。

首先，在 Web 浏览器中导航到 [Azure 门户](https://portal.azure.com)。 单击“新建”，搜索“规模集”，然后选择“虚拟机规模集”条目：

![azure 虚拟机规模集门户搜索](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>创建规模集
现在可使用默认设置并快速创建规模集。

* 输入规模集的名称。  
此名称将成为规模集前端负载均衡器的 FQDN 的基础，因此请确保在所有 Azure 中，此名称是唯一的。

* 选择所需的 OS 类型。

* 输入所需的用户名，并选择首选的身份验证类型。  
如果选择使用密码，密码的长度必须至少为 12 个字符，并且必须符合以下四个复杂性要求中的其中三个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 查看更多关于 [用户名和密码要求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)。 如果选择“SSH 公钥”，请确保仅在公钥中粘贴，而不是在私钥中：

* 选择“是”或“否”来“启用超出 100 个实例的缩放”。  
如果选择“是”，则规模集可以跨多个放置组。 有关详细信息，请参阅[此文档](./virtual-machine-scale-sets-placement-groups.md)。

* 请确保选择适当的实例大小。  
有关虚拟机大小的详细信息，请参阅 [Windows VM 大小](..\virtual-machines\windows\sizes.md)或 [Linux VM 大小](..\virtual-machines\linux\sizes.md)。

* 输入所需的资源组名称和位置。  
如果你的区域和实例大小支持可用性区域，则“可用性区域”字段处于启用状态。 有关可用性区域的详细信息，请参阅此[概述](../availability-zones/az-overview.md)文章。

* 输入所需的域名标签（规模集前端负载均衡器的 FQDN 的基础）。  
此标签在全 Azure 中必须是唯一的。

* 选择所需的操作系统磁盘映像、实例计数和计算机大小。

* 选择所需磁盘类型：托管或非托管。  
有关详细信息，请参阅[此文档](./virtual-machine-scale-sets-managed-disks.md)。 如果选择让规模集跨越多个放置组，则此选项不可用，因为需要托管磁盘才能使规模集跨越放置组。

* 启用或禁用自动缩放，如果启用，请进行配置。

![azure 虚拟机规模集门户创建提示](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>连接到规模集中的 VM
如果选择将规模集限制为单个放置组，则在部署规模集时配置 NAT 规则，以便可以轻松连接到规模集（如果不是这样，则要连接到规模集中的虚拟机，可能需要在与规模集相同的虚拟网络中创建 jumpbox）。 若要查看它们，请导航到规模集的负载均衡器的 `Inbound NAT Rules` 选项卡：

![azure 虚拟机规模集门户 nat 规则](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

可以使用这些 NAT 规则连接到规模集中的每个 VM。 例如，对于 Windows 规模集，如果传入端口 50000 使用的是 NAT 规则，则可以通过 `<load-balancer-ip-address>:50000` 上的 RDP 连接到该计算机。 对于 Linux 规模集，则使用命令 `ssh -p 50000 <username>@<load-balancer-ip-address>` 进行连接。

## <a name="next-steps"></a>后续步骤
有关如何从 CLI 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-cli-quick-create.md)。

有关如何从 PowerShell 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-windows-create.md)。

有关如何从 Visual Studio 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-vs-create.md)。

有关常规文档，请参阅[规模集的文档概述页](virtual-machine-scale-sets-overview.md)。

有关一般信息，请参阅[规模集的主要登陆页](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

