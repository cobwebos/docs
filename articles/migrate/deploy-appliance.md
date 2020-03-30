---
title: 设置 Azure 迁移应用程序
description: 了解如何设置 Azure 迁移设备以评估和迁移 VMware VM。
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337657"
---
# <a name="set-up-an-azure-migrate-appliance"></a>已设置一个 Azure Migrate 设备

本文总结了设置 Azure 迁移设备的选项。 

Azure 迁移设备是本地的轻量级设备部署，用于多种方案。

**方案** | **详细信息**
--- | ---
使用 Azure 迁移评估 VMware VM：服务器评估 | 发现 VM 应用和依赖项<br/><br/> 收集机器元数据和性能元数据进行评估。
使用 Azure 迁移评估超 VM：服务器评估 | 发现超虚拟机<br/><br/> 收集机器元数据和性能元数据进行评估。
评估物理机器 | 发现计算机为物理服务器<br/><br/> 收集机器元数据和性能元数据进行评估。
使用无代理迁移复制 VMware VM。 | 复制 VMware VM，而无需在要复制的 VM 上安装任何内容。


## <a name="deployment-options"></a>部署选项

您可以通过多种方式部署设备。

**方案** | **模板** | **脚本** 
--- | --- | --- | ---
**访问 VMware VM** | 使用下载的 OVA 模板进行部署。<br/><br/> 了解如何[使用模板](how-to-set-up-appliance-vmware.md)部署设备，或启动[评估教程](tutorial-prepare-vmware.md)，并在本教程期间使用模板部署设备。  | 使用 PowerShell 安装程序脚本进行部署。<br/><br/>  [查看](deploy-appliance-script.md)设备脚本说明。
**评估 Hyper-V VM** | 使用下载的 VHD 模板进行部署。 <br/><br/> 了解如何[使用模板](how-to-set-up-appliance-vmware.md)部署设备，或启动[评估教程](tutorial-prepare-vmware.md)，并在本教程期间使用模板部署设备。 | 使用 PowerShell 安装程序脚本进行部署。<br/><br/> [查看](deploy-appliance-script.md)设备脚本说明。 
**评估物理服务器** | 没有模板。 | 使用 PowerShell 安装程序脚本进行部署。<br/><br/> 查看[设备脚本说明](how-to-set-up-appliance-physical.md)，或启动[评估教程](tutorial-prepare-physical.md)，并在本教程中部署设备。
**复制 VMware VM（无代理）** | 使用下载的 OVA 模板进行部署。<br/><br/> 如果您已经评估了复制的 VM，则已在评估期间设置了设备。<br/><br/> 如果在不对其进行评估的情况下复制 VMware VM，请了解如何使用模板部署设备，或启动[无代理迁移教程](tutorial-migrate-vmware.md)，并在本教程期间使用模板部署设备。 | 使用 PowerShell 安装程序脚本进行部署。 <br/><br/> [查看](deploy-appliance-script.md)设备脚本说明。 




## <a name="next-steps"></a>后续步骤

[查看](migrate-appliance.md)设备要求。