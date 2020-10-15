---
title: 使用堡垒远程工作： Azure 堡垒
description: 本页介绍在 COVID-19（新冠病毒）疫情期间如何利用 Azure Bastion 来实现远程工作。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077943"
---
# <a name="working-remotely-using-azure-bastion"></a>使用 Azure Bastion 进行远程工作

Azure Bastion 通过允许具有 Internet 连接的用户访问 Azure 虚拟机，在支持远程工作方案方面起着关键作用。 特别是，它使 IT 管理员能够在全球各地随时随地管理他们在 Azure 上运行的应用程序。

>[!NOTE]
>本文介绍了如何利用 Azure 堡垒、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作，并减少因 COVID-19 危机而面临的网络问题。
>

## <a name="securely-access-virtual-machines"></a>安全地访问虚拟机

具体地说，Azure Bastion 直接在 Azure 门户中为 Azure 虚拟网络中的虚拟机提供安全无缝的 RDP/SSH 连接，而无需使用公共 IP 地址。 若要详细了解 Azure Bastion 体系结构和主要功能，请参阅[什么是 Azure Bastion](bastion-overview.md)。

Azure Bastion 按虚拟网络部署，这意味着公司可以配置和管理一个 Azure Bastion，以快速支持远程用户访问 Azure 虚拟网络中的虚拟机。 有关如何创建和管理 Azure Bastion 的指导，请参阅[创建堡垒主机](./tutorial-create-host-portal.md)。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](./tutorial-create-host-portal.md)、[PowerShell](bastion-create-host-powershell.md) 或 Azure CLI 配置 Azure Bastion。

* 有关其他信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。