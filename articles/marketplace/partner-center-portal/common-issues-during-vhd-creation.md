---
title: 创建 VHD 期间出现的常见问题 (FAQ)
description: 有关创建虚拟硬盘（VHD）时常见问题的常见问题。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266261"
---
# <a name="common-issues-during-vhd-creation"></a>在 VHD 创建期间遇到的常见问题

> [!NOTE]
> 我们正在将 Azure VM 产品/服务的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移你的产品/服务之前，请继续按照云合作伙伴门户中的在[VHD 创建期间遇到的常见问题](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)中的说明来管理你的产品/服务。

这些常见问题（FAQ）涵盖了在为 Azure 虚拟机产品/服务创建虚拟硬盘（VHD）时可能遇到的常见问题。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何实现使用高级存储中的 VHD 从 Azure 门户创建 VM？

Azure Marketplace 当前不支持从托管存储或 Azure 高级存储中的映像创建 VM 产品/服务。 有关详细信息，请参阅[Azure 托管磁盘概述](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>能否使用第2代 Vm 提供服务？

否，仅支持第1代 Vhd。 但是，我们目前正在与 Microsoft Azure 平台团队合作，调查对第2代 Vm 的支持。 有关详细信息，请参阅是否[应在 hyper-v 中创建第1代或第2代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何更改主机名称？

无法迁移。 创建 VM 后，用户（包括所有者）无法更新主机名。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何实现重置远程桌面服务或其登录密码？

这些文章介绍了如何为基于 Windows 和 Linux 的 Vm 执行 RDS 重置：

* [如何在 Windows VM 中重置远程桌面服务或其登录密码](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 扩展重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何实现生成新的 SSH 证书？

[AZURE VM 映像认证](https://aks.ms/CertifyVMimage)中介绍了证书的生成。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何实现配置虚拟专用网络（VPN）以使用我的 Vm？

如果使用 Azure 资源管理器部署模型，则有三个选项：

* [使用 Azure 门户创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [使用 Azure PowerShell 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [使用 CLI 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>有关在基于 Azure 的 VM 上运行 Microsoft 服务器软件的 Microsoft 支持策略有哪些？

你可以在[Microsoft 服务器软件支持 Microsoft Azure 虚拟机](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)上找到详细信息。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虚拟机是否有关联的唯一标识符？

如果托管在 Azure 上，则有。 Azure 会为创建的每个新 VM 资源分配一个名为[Azure 虚拟机唯一 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)的唯一标识符。 有关详细信息，请参阅 Azure 虚拟机唯一 ID。 还可以通过[列表 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)获取此标识符。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何在启动任务中管理自定义脚本扩展？

若要详细了解如何使用 Azure PowerShell 模块、Azure 资源管理器模板以及 Windows 系统上的故障排除步骤，请参阅适用于[windows 的自定义脚本扩展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace 中是否支持32位应用程序或服务？

一般来说是不可以的。 Azure VM 支持的操作系统和标准服务均为 64 位。 尽管大多数64位操作系统都支持32位版本的应用程序以实现向后兼容性，但不支持将32位应用程序用作 VM 解决方案的一部分，因此强烈建议不要使用它。 将你的应用程序重新创建为64位项目。

有关详细信息，请参阅以下文章：

* [运行 32 位应用程序](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 虚拟机对 32 位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>错误：已将 VHD 作为资源注册到映像存储库

每次尝试从 Vhd 创建映像时，会在 Azure PowerShell 中收到 "VHD 已作为资源注册到映像存储库" 错误。 我在 Azure 中找不到任何图像，也没在 Azure 中找到此名称。 如何解决此问题？

如果从具有锁定的 VHD 中创建了 VM，通常会出现此问题。 确认未从此 VHD 分配 VM，然后重试该操作。 如果此问题仍然存在，请打开支持票证。 请参阅[合作伙伴中心支持](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。
