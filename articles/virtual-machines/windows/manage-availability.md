---
title: 在 Azure 中管理 Windows Vm 的可用性
description: 了解如何使用多个虚拟机来确保 Windows 应用程序在 Azure 中的高可用性
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 354cd8d291f8ad5c842d33b5ac63b0deb58b7d41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870046"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>在 Azure 中管理 Windows 虚拟机的可用性 

了解如何设置和管理多个虚拟机，以确保 Windows 应用程序在 Azure 中的高可用性。 也可以[管理 Linux 虚拟机的可用性](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>后续步骤
若要了解对虚拟机进行负载均衡的详细信息，请参阅[对虚拟机进行负载均衡](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

查看在 IaaS 中 SQL Server 上运行 N 层应用程序的参考体系结构

* [Azure 上包含 SQL Server 的 Windows N 层应用程序](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [在多个 Azure 区域中运行 N 层应用程序以确保高可用性](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
