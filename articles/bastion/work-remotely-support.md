---
title: 使用堡垒远程工作：Azure 堡垒
description: 本页介绍如何利用 Azure 堡垒启用由于 COVID-19 大流行而远程工作。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619384"
---
# <a name="working-remotely-using-azure-bastion"></a>使用 Azure 堡垒远程工作

Azure Bastion 通过允许具有 Internet 连接的用户访问 Azure 虚拟机，在支持远程工作方案方面发挥着关键作用。 特别是，它使 IT 管理员能够随时随地管理在 Azure 上运行的应用程序。

>[!NOTE]
>本文介绍如何利用 Azure 堡垒、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作，并缓解因 COVID-19 危机而面临的网络问题。
>

## <a name="securely-access-virtual-machines"></a>安全访问虚拟机

具体而言，Azure Bastion 提供安全、无缝的 RDP/SSH 连接，直接在 Azure 门户中与 Azure 虚拟网络中的虚拟机连接，而无需使用公共 IP 地址。 有关 Azure 堡垒体系结构和关键功能的详细信息，请查看什么是[Azure 堡垒](bastion-overview.md)。

Azure 堡垒部署于每个虚拟网络，这意味着公司可以配置和管理一个 Azure 堡垒，以快速支持远程用户访问 Azure 虚拟网络中的虚拟机。 有关如何创建和管理 Azure 堡垒的指导，请参阅[创建堡垒主机](bastion-create-host-portal.md)。

## <a name="next-steps"></a>后续步骤

* 使用[Azure 门户](bastion-create-host-portal.md)、[电源外壳](bastion-create-host-powershell.md)或 Azure CLI 配置 Azure 堡垒。

* 有关详细信息，请阅读[堡垒常见问题解答](bastion-faq.md)。
