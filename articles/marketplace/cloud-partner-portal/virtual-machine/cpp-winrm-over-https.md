---
title: 针对 Azure 通过 HTTPS Windows 远程管理 |Azure Marketplace
description: 介绍如何配置 Azure 托管的基于 Windows 的 VM，以便可以使用 PowerShell 进行远程管理。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: e39f83b2ed715afbfff69770c151cfc4d527105d
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132258"
---
# <a name="windows-remote-management-over-https"></a>基于 HTTPS 的 Windows 远程管理

本节介绍如何配置 Azure 托管的基于 Windows 的 VM，以便可以使用 PowerShell 对其进行远程管理和部署。  要启用 PowerShell 远程处理，目标 VM 必须公开 Windows 远程管理 (WinRM) HTTPS 终结点。  有关 PowerShell 远程处理的详细信息，请参阅[运行远程命令](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands)。  有关 WinRM 的详细信息，请参阅 [Windows 远程管理](https://docs.microsoft.com/windows/desktop/WinRM/portal)。

如果使用“经典”Azure 方法之一 - Azure 服务管理器门户或弃用的 [Azure 服务管理 API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) - 来创建 VM，会自动为其配置 WinRM 终结点。  但是，如果使用以下任何“新式”Azure 方法创建 VM，则 VM 不会配置为通过 HTTPS 实现 WinRM。

- 使用 [Azure 门户](https://portal.azure.com/)，通常需要通过批准的基础映像来实现，如[创建与 Azure 兼容的 VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)部分所述
- [使用 Azure 资源管理器模板](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- 使用 Azure PowerShell 或 Azure CLI 命令外壳。  有关示例，请参阅[快速入门：使用 PowerShell 在 Azure 中创建 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)和[快速入门：使用 Azure CLI 创建 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)。

为载入 VM 而运行认证工具包时也需要此 WinRM 终结点，如[认证 VM 映像](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)所述。

相比之下，通常使用 [Azure CLI](https://docs.microsoft.com/cli/azure) 或 Linux 命令，从 SSH 控制台远程管理 Linux VM。  Azure 还提供其他几种[在 Linux VM 中运行脚本](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)的方法。  对于更复杂的方案，有多种自动化和集成解决方案可用于基于 Windows 或 Linux 的 VM。


## <a name="configure-and-deploy-with-winrm"></a>使用 WinRM 进行配置和部署

可以在两个不同的开发阶段配置基于 Windows 的 VM 的 WinRM 端点：

- 在创建期间 - 在将 VM 部署到现有 VHD 期间。  这是新套餐的首选方法。  此方法需要创建 Azure 证书，使用提供的 Azure 资源管理器模板并运行自定义的 PowerShell 脚本。
- 在 Azure 上托管的现有 VM 上完成部署后。  若已在 Azure 上部署了 VM 解决方案，并且需要为其启用 Windows 远程管理，请使用此方法。  此方法需要在 Azure 门户中进行手动更改以及在目标 VM 上执行脚本。


## <a name="next-steps"></a>后续步骤
如果要创建新 VM，则可以在从其 VHD [部署 VM 期间启用 WinRM](./cpp-deploy-vm-vhd.md)。  否则，可以在现有 VM 中启用 WinRM
