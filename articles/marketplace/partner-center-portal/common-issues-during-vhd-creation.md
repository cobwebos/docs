---
title: 创建 VHD 期间出现的常见问题 (FAQ)
description: 有关创建虚拟硬盘 (VHD) 的常见问题解答。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: emuench
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 2b6ab5d36cd5a1f66badc79d1b2d42e464d028f4
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110736"
---
# <a name="common-issues-during-vhd-creation"></a>创建 VHD 期间出现的常见问题

这些常见问题 (FAQ) 涵盖了在为 Azure 虚拟机产品/服务创建虚拟硬盘 (VHD) 时可能遇到的一般问题。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何使用高级存储中的 VHD 从 Azure 门户创建 VM？

Azure 市场目前不支持基于托管存储或 Azure 高级存储中的映像创建 VM 产品/服务。 有关详细信息，请参阅 [Azure 托管磁盘概述](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>是否可将第 2 代 VM 用于产品/服务？

不可以，仅支持第 1 代 VHD。 但是，我们目前正在与 Microsoft Azure 平台团队协作，以研究支持第 2 代 VM 的可行性。 有关详细信息，请参阅[是否应在 Hyper-V 中创建第 1 代或第 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何更改主机名称？

你无法更改。 创建 VM 后，用户（包括所有者）无法更新主机名称。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重置远程桌面服务或其登录密码？

以下文章介绍了如何对基于 Windows 和基于 Linux 的 VM 执行 RDS 重置：

* [如何在 Windows VM 中重置远程桌面服务或其登录密码](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 扩展重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何生成新的 SSH 证书？

[Azure VM 映像认证](https://aks.ms/CertifyVMimage)中介绍了生成证书的信息。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何将虚拟专用网络 (VPN) 配置为使用我的 VM？

如果使用 Azure 资源管理器部署模型，则有三个选项：

* [使用 Azure 门户创建基于路由的 VPN 网关](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [使用 Azure PowerShell 创建基于路由的 VPN 网关](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [使用 CLI 创建基于路由的 VPN 网关](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>有关在基于 Azure 的 VM 上运行 Microsoft 服务器软件的 Microsoft 支持策略有哪些？

有关详细信息，请访问 [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虚拟机是否有关联的唯一标识符？

如果托管在 Azure 上，则有。 Azure 会将称作 [Azure 虚拟机唯一 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) 的唯一标识符分配到所创建的每个新 VM 资源。 有关详细信息，请参阅《Azure 虚拟机唯一 ID》。 还可以通过[列表 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) 获取此标识符。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中如何管理启动任务中的自定义脚本扩展？

有关通过 Azure PowerShell 模块、Azure 资源管理器模板和 Windows 系统上的故障排除步骤使用自定义脚本扩展的详细信息，请参阅[适用于 Windows 的自定义脚本扩展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure 市场是否支持 32 位应用程序或服务？

一般来说是不可以的。 Azure VM 支持的操作系统和标准服务均为 64 位。 尽管大多数 64 位操作系统都支持 32 位版本的应用程序以实现向后兼容性，但不支持将 32 位应用程序用作 VM 解决方案的一部分，因此强烈建议不要使用 32 位应用程序。 请将应用程序重新创建为 64 位项目。

有关详细信息，请参阅以下文章：

* [运行 32 位应用程序](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 虚拟机中 32 位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>错误：VHD 已作为资源注册到映像存储库

每当我尝试从 VHD 创建映像时，都会在 Azure PowerShell 中收到错误“VHD 已作为资源注册到映像存储库”。 我之前没有创建任何映像，也没在 Azure 中找到任何带有此名称的映像。 如何解决此问题？

如果从带锁的 VHD 创建了 VM，则通常会出现此问题。 请确认没有从此 VHD 分配 VM，然后重试此操作。 如果问题仍然存在，请开具支持票证。 请参阅[合作伙伴中心支持](support.md)。
