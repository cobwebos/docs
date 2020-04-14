---
title: 创建 VHD 期间出现的常见问题 (FAQ)
description: 在创建虚拟硬盘 （VHD） 时，经常询问的常见问题。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266261"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 创建过程中的常见问题

> [!NOTE]
> 我们将 Azure VM 产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请继续按照云合作伙伴门户中的[VHD 创建 （FAQ） 中常见问题](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)中的说明进行管理您的产品/服务。

这些常见问题 （FAQ） 涵盖了为 Azure 虚拟机产品创建虚拟硬盘 （VHD） 时可能会遇到的常见问题。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何使用高级存储中的 VHD 从 Azure 门户创建 VM？

Azure 应用商店当前不支持从托管存储或 Azure 高级存储上的图像创建 VM 产品/ 有关详细信息，请参阅[Azure 托管磁盘概述](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>我可以使用第 2 代 VM 进行优惠吗？

否，仅支持第 1 代 VHD。 但是，我们当前正在与 Microsoft Azure 平台团队合作，调查对第 2 代 VM 的支持。 有关详细信息，请参阅[是否应在 Hyper-V 中创建第 1 代或第 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何更改主机名称？

无法迁移。 创建 VM 后，用户（包括所有者）无法更新主机名。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重置远程桌面服务或其登录密码？

这些文章解释了如何对基于 Windows 和 Linux 的 VM 执行 RDS 重置：

* [如何在 Windows VM 中重置远程桌面服务或其登录密码](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 扩展重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何生成新的 SSH 证书？

证书的生成在[Azure VM 映像认证](https://aks.ms/CertifyVMimage)中解释。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何配置虚拟专用网络 （VPN） 以使用 VM？

如果使用 Azure 资源管理器部署模型，则有三个选项：

* [使用 Azure 门户创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [使用 Azure PowerShell 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [使用 CLI 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>有关在基于 Azure 的 VM 上运行 Microsoft 服务器软件的 Microsoft 支持策略有哪些？

您可以在微软 Azure[虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)中找到详细信息。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虚拟机是否有关联的唯一标识符？

如果托管在 Azure 上，则有。 Azure 将唯一标识符（称为[Azure 虚拟机唯一 ID）](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)分配给创建的每个新 VM 资源。 有关详细信息，请参阅 Azure 虚拟机唯一 ID。 您还可以通过[列表 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)获取此标识符。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何管理启动任务中的自定义脚本扩展？

有关使用 Azure PowerShell 模块、Azure 资源管理器模板以及 Windows 系统上的故障排除步骤使用自定义脚本扩展的详细信息，请参阅[Windows 的自定义脚本扩展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure 应用商店中是否支持 32 位应用程序或服务？

一般来说是不可以的。 Azure VM 支持的操作系统和标准服务均为 64 位。 虽然大多数 64 位操作系统都支持 32 位版本的应用程序，以便向后兼容，但不支持并且强烈建议使用 32 位应用程序作为 VM 解决方案的一部分。 将应用程序重新创建为 64 位项目。

有关详细信息，请参阅以下文章：

* [运行 32 位应用程序](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 虚拟机对 32 位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>错误：VHD 已注册到映像存储库作为资源

每次尝试从 VHD 创建映像时，都会在 Azure PowerShell 中收到错误"VHD 已在映像存储库中注册为资源"。 我以前没有创建任何图像，也没有在 Azure 中找到任何具有此名称的图像。 如何解决此问题？

如果从具有锁定的 VHD 创建 VM，则通常会出现此问题。 确认没有从此 VHD 分配 VM，然后重试该操作。 如果此问题仍然存在，则打开支持票证。 请参阅[合作伙伴中心的支持](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。
