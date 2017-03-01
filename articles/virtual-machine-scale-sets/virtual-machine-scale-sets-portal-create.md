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
ms.date: 09/15/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 35d4c8593dd9698017df85675395430f345f4e86
ms.openlocfilehash: 17054073e921708cc0c9392ed1b94e9579a9f940
ms.lasthandoff: 02/18/2017


---
# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>使用 Azure 门户创建虚拟机规模集
本教程介绍如何通过 Azure 门户在数分钟内轻松创建虚拟机规模集。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/) 。

## <a name="choose-the-vm-image-from-the-marketplace"></a>从应用商店中选择 VM 映像
在门户中，可以使用 CentOS、CoreOS、Debian、Open Suse、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、Ubuntu Server 或 Windows Server 映像轻松部署规模集。

首先，在 Web 浏览器中导航到 [Azure 门户](https://portal.azure.com)。 单击 `New`，搜索 `scale set`，然后选择 `Virtual machine scale set` 条目：

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-scale-set"></a>创建规模集
现在可使用默认设置并快速创建规模集。

* 在 `Basics` 边栏选项卡上，输入规模集的名称。 此名称将成为规模集前端负载均衡器的 FQDN 的基础，因此请确保在所有 Azure 中，此名称是唯一的。
* 选择所需的 OS 类型，输入所需的用户名，然后选择首选的身份验证类型。 如果选择使用密码，密码的长度必须至少为 12 个字符，并且必须符合以下四个复杂性要求中的其中三个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 了解有关[用户名和密码要求](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)的详细信息。 如果选择 `SSH public key`，请确保仅在公钥中粘贴，而不是在私钥中：

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* 选择是否要将规模集设置为单个组或它是否应跨越多个放置组。 允许规模集跨越放置组可以实现容量上超过 100 个 VM 的规模集（最多 1,000），不过具有某些限制。 有关详细信息，请参阅[此文档](./virtual-machine-scale-sets-placement-groups.md)。
* 输入所需的资源组名称和位置，然后单击 `OK` 。
* 在 `Virtual machine scale set service settings` 边栏选项卡上：输入所需的域名标签（规模集前端负载均衡器的 FQDN 的基础）。 此标签在全 Azure 中必须是唯一的。
* 选择所需的操作系统磁盘映像、实例计数和计算机大小。
* 选择所需磁盘类型：托管或非托管。 有关详细信息，请参阅[此文档](./virtual-machine-scale-sets-managed-disks.md)。 如果选择让规模集跨越多个放置组，则此选项不可用，因为需要托管磁盘才能使规模集跨越放置组。
* 启用或禁用自动缩放，如果启用，请按照以下步骤进行配置：

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* 当验证完成时，在 `Summary` 边栏选项卡上，单击 `OK` 开始规模集部署。


## <a name="connect-to-a-vm-in-the-scale-set"></a>连接到规模集中的 VM
如果选择将规模集限制为单个放置组，则在部署规模集时配置 NAT 规则，以便可以轻松连接到规模集（如果不是这样，则若要连接到规模集中的虚拟机，可能需要在与规模集相同的虚拟网络中创建 jumpbox）。 若要查看它们，请导航到规模集的负载均衡器的 `Inbound NAT Rules` 选项卡：

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

可以使用这些 NAT 规则连接到规模集中的每个 VM。 例如，对于 Windows 规模集，如果传入端口 50000 使用的是 NAT 规则，则可以通过 `<load-balancer-ip-address>:50000` 上的 RDP 连接到该计算机。 对于 Linux 规模集，则使用命令 `ssh -p 50000 <username>@<load-balancer-ip-address>` 进行连接。

## <a name="next-steps"></a>后续步骤
有关如何从 CLI 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-cli-quick-create.md)。

有关如何从 PowerShell 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-windows-create.md)。

有关如何从 Visual Studio 部署规模集的文档，请参阅[本文档](virtual-machine-scale-sets-vs-create.md)。

有关常规文档，请参阅[规模集的文档概述页](virtual-machine-scale-sets-overview.md)。

有关一般信息，请参阅[规模集的主要登陆页](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。


