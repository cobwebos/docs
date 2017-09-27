---
title: "排查在 Azure 中部署 Windows 虚拟机时遇到的问题 | Microsoft Docs"
description: "排查在 Azure Resource Manager 部署模型中部署 Windows 虚拟机时遇到的问题。"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/22/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 2180a11c53a3b283fed35844c5821aef744fb95e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/11/2017

---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>排查在 Azure 中部署 Windows 虚拟机时遇到的问题

若要排查 Azure 中虚拟机 (VM) 的部署问题，请查看[热门问题](#top-issues)了解常见故障和解决方法。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。

## <a name="top-issues"></a>常见问题
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>群集无法支持请求的 VM 大小
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 以更小的 VM 大小重试请求。
- 如果无法更改请求的 VM 大小：
    - 停止可用性集中的所有 VM。 依次单击“资源组”> 资源组 >“资源”> 可用性集 >“虚拟机”> 虚拟机 >“停止”。
    - 在所有 VM 都停止后，创建相应大小的 VM。
    - 先启动新 VM，再选择所有已停止的 VM 并单击“启动”。


## <a name="the-cluster-does-not-have-free-resources"></a>群集没有可用资源
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 请稍后重试请求。
- 如果新 VM 属于不同的可用性集
    - 在同一区域的其他可用性集中创建 VM。
    - 将新 VM 添加到同一虚拟网络。

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>如何使用 Windows 客户端映像并将其部署到 Azure？

如果有相应的 Visual Studio（以前为 MSDN）订阅，可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 开展开发/测试方案。 [本文](client-images.md)概述在 Azure 中运行 Windows 客户端和使用 Azure 库映像所要满足的条件。

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>如何使用混合使用权益 (HUB) 部署虚拟机？

有多种不同方法可使用 Azure 混合使用权益部署 Windows 虚拟机。

对于企业协议订阅：

•   可从通过 Azure 混合使用权益预配的特定 Marketplace 映像中部署 VM。

对于企业协议：

•   可以上传自定义 VM，并使用 Resource Manager 模板或 Azure PowerShell 进行部署。

有关详细信息，请参阅以下资源：

 - [Azure 混合使用权益概述](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [可下载的常见问题解答](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [适用于 Windows Server 和 Windows 客户端的 Azure 混合使用权益](hybrid-use-benefit-licensing.md)。

 - [如何在 Azure 中使用混合使用权益](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>如何激活 Visual Studio Enterprise (BizSpark) 的每月信用额度

若要激活每月信用额度，请参阅此[文章](https://azure.microsoft.com/offers/ms-azr-0064p/)。

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>如何将企业开发/测试添加到我的企业协议 (EA) 以访问 Windows 客户端映像？

只有企业管理员授予根据企业开发/测试产品/服务创建订阅权限的帐户所有者才能这样做。 帐户所有者通过 Azure 帐户门户创建订阅，并应随后将活跃 Visual Studio 订阅者作为联合管理员进行添加， 以便他们能够管理和使用开发及测试所需的资源。 有关详细信息，请参阅[企业开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)。

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>我的 Windows N 系列 VM 缺少驱动程序

基于 Windows 的 VM 的驱动程序位于[此处](n-series-driver-setup.md)。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>在我的 N 系列 VM 中找不到 GPU 实例

若要利用运行 Windows Server 2016 或 Windows Server 2012 R2 的 Azure N 系列 VM 的 GPU 功能，在部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 针对 [Windows VM](n-series-driver-setup.md) 和 [Linux VM](../linux/n-series-driver-setup.md) 提供了驱动程序安装信息。

## <a name="are-client-images-supported-for-n-series"></a>N 系列是否支持客户端映像？

目前，Azure 仅支持运行 Windows Server 和 Linux 操作系统的 VM 上的 N 系列。

## <a name="is-n-series-vms-available-in-my-region"></a>我所在的地区是否提供 N 系列 VM？

可以从[可用产品(按区域)](https://azure.microsoft.com/regions/services) 以及[此处](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)的定价来查看可用性。

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>可以在 Azure 中使用和部署哪些客户端映像，以及如何获取它们？

如果有相应的 Visual Studio（以前为 MSDN）订阅，可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 开展开发/测试方案。 

- Azure 库的[符合条件的开发/测试产品/服务](client-images.md#eligible-offers)中提供了 Windows 10 映像。 
- 属于任一产品类型的 Visual Studio 订户也可以[适当地准备和创建](prepare-for-upload-vhd-image.md) 64 位 Windows 7、Windows 8 或 Windows 10 映像，并[上载到 Azure](upload-generalized-managed.md)。 仅限有效的 Visual Studio 订户用于开发/测试目的。

[本文](client-images.md)概述在 Azure 中运行 Windows 客户端和使用 Azure 库映像所要满足的条件。

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>在调整 VM 的大小时，我看不到我需要的 VM 大小系列。

运行的 VM 部署到物理服务器。 Azure 区域中的物理服务器被分入常见物理硬件群集中。 需要将 VM 移到其他硬件群集才能重设 VM 大小，具体操作因部署 VM 所用部署模型而异。

- 对于在经典部署模型中部署的 VM，必须删除并重新部署云服务部署，才能将 VM 大小更改为其他大小系列。

- 对于在 Resource Manager 部署模型中部署的 VM，必须先停止可用性集中的所有 VM，才能更改可用性集中任何 VM 的大小。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>在可用性集中部署时，列出的 VM 大小不受支持。

请选择可用性集的群集支持的大小。 建议在创建可用性集时，选择所需要的最大 VM 大小，并将其作为到可用性集的第一次部署。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>是否可以将现有的经典 VM 添加到可用性集？

是的。 可以将现有经典 VM 添加到新的或现有的可用性集。 有关详细信息，请参阅[将现有虚拟机添加到可用性集](classic/configure-availability.md#addmachine)。


## <a name="next-steps"></a>后续步骤
如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。

或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

