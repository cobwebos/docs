---
title: 在 Azure marketplace (FAQ) 创建 VHD 期间出现的常见问题
description: 有关创建 VHD 和相关操作的常见问题解答。
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: d8b532793282db18d6182237bb921bc118ea717b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938454"
---
# <a name="common-issues-during-vhd-creation-faq"></a>创建 VHD 期间出现的常见问题 (FAQ)

以下常见问题解答 (FAQ) 涵盖了在为 VM 套餐创建虚拟硬盘 (VHD) 和虚拟机 (VM) 期间遇到的常见问题。 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>如何使用上传到高级存储的 VHD 在 Azure 门户中创建 VM？

Azure 市场目前不支持基于托管存储或 Azure 高级存储中的映像创建 VM 套餐。  有关这些存储选项的详细信息，请参阅 [Azure 托管磁盘概述](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。


## <a name="can-you-use-generation-2-vms-for-offers"></a>是否可将第 2 代 VM 用于套餐？

不可以，仅支持第 1 代 VHD。  但是，我们目前正在与 Microsoft Azure 平台团队协作，以研究第 2 代 VM 的支持可行性。  有关差异的详细信息，请参阅[应在 Hyper-V 中创建第 1 代还是第 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>如何更改主机的名称？

无法更改。  创建 VM 后，用户（包括所有者）无法更新主机的名称。


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重置远程桌面服务或其登录密码？

以下文章介绍了如何对基于 Windows 和基于 Linux 的 VM 执行 RDS 重置：   

- [如何在 Windows VM 中重置远程桌面服务或其登录密码](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [如何使用 VMAccess 扩展重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>如何生成新的 SSH 证书？

[获取 VM 映像的共享访问签名 URI](./cpp-get-sas-uri.md) 一文的后续部分[为 VM 套餐创建技术资产](./cpp-create-technical-assets.md)中介绍了如何生成证书。


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何将虚拟专用网络 (VPN) 配置为使用我的 VM？

如果使用 Azure 资源管理器部署模型，则可以通过三个常用选项来设置 VPN：
- [使用 Azure 门户创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [使用 PowerShell 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [使用 CLI 创建基于路由的 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>有关在基于 Azure 的 VM 上运行 Microsoft 服务器软件的 Microsoft 支持策略有哪些？

[Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)一文详细介绍了这些策略支持。


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虚拟机是否有关联的唯一标识符？

如果托管在 Azure 上，则有。  Azure 会将称作“Azure 虚拟机唯一 ID”的唯一标识符分配到所创建的每个新 VM 资源。  有关详细信息，请阅读博客文章 [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)（Azure 虚拟机唯一 ID）。  也可以通过[列表 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) 以编程方式获取此标识符。


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中如何管理启动任务中的自定义脚本扩展？

下文详细说明如何通过 Azure PowerShell 模块、Azure 资源管理器模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤：[适用于 Windows 的自定义脚本扩展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Azure 市场是否支持 32 位应用程序或服务？

一般来说是不可以的。  Azure VM 支持的操作系统和标准服务均为 64 位。  但是，从技术角度看，为了向后兼容，大多数 64 位操作系统都支持运行 32 位版本的应用程序。  但是，不支持在 VM 解决方案中使用 32 位应用程序，因此，我们强烈建议不要使用 32 位版本。   请将应用程序重新编译为 64 位项目。

有关详细信息，请参阅以下文章：
- [运行 32 位应用程序](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 虚拟机中 32 位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>每当我尝试从 VHD 创建映像时，PowerShell 中都会出现错误 `.VHD is already registered with image repository as the resource`。 我之前没有创建任何图像，也没在 Azure 中找到任何带有此名称的图像。 如何解决此问题？

如果用户从带锁的 VHD 预配了 VM，则通常会出现此问题。  请确认没有从此 VHD 分配 VM，然后重试操作。  如果此问题仍旧出现，请根据[云合作伙伴门户支持](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)中所述开具支持票证。 

