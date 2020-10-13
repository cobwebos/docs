---
title: Azure 上的机密计算虚拟机
description: 了解 Intel SGX 硬件，以启用机密计算工作负荷。
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: a7e3ade66aa4ebf7584e03b75f85c48b44537d97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994852"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure 机密计算虚拟机 (Vm) 概述


Azure 是在虚拟化环境中提供机密计算的第一家云提供商。 我们开发了充当硬件与应用程序之间的抽象层的虚拟机。 你可以使用冗余和可用性选项大规模运行工作负荷。  

## <a name="intel-sgx-enabled-virtual-machines"></a>支持 Intel SGX 的虚拟机

在 Azure 机密计算虚拟机中，为应用程序中的一部分代码和数据保留了一部分 CPU 硬件。 此受限部分即为领地。 

![VM 模型](media/overview/hardware-backed-enclave.png)

Azure 机密计算基础结构目前由虚拟机 (VM) 的专用 SKU 组成。 这些 VM 在具有软件防护扩展 (Intel SGX) 的 Intel 处理器上运行。 [Intel SGX](https://intel.com/sgx) 组件可以通过机密计算来增强保护。 

当前，Azure 提供了基于 Intel SGX 技术的 [DCsv2 系列](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)来用于创建基于硬件的领地。 你可以构建安全的基于领地的应用程序以在 DCsv2 系列 VM 中运行，从而保护使用中的应用程序数据和代码。 

[阅读](virtual-machine-solutions.md) 有关部署 Azure 机密计算虚拟机的详细信息，并提供基于硬件的信任 enclaves。

## <a name="enclaves"></a>指定位址空间

Enclaves 是硬件处理器和内存中的安全部分。 即使是使用调试器，也无法查看领地内部的数据或代码。 如果不受信任的代码尝试修改领地内存中的内容，则会禁用该环境并拒绝操作。

从根本上讲，可将领地视为一个安全盒。 加密的代码和数据将放入该盒子中。 在盒子的外部看不到任何内容。 你为领地指定一个用于解密数据的密钥，然后，在从领地发出数据之前，会再次对数据进行处理和加密。

每个 enclave 都有一组的加密页面缓存 (EPC) ，确定每个 enclave 可以容纳的内存量。 较大的 DCsv2 虚拟机具有更多的 EPC 内存。 对于每个 VM 大小的最大 EPC，请阅读 [DCsv2 规范](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) 页。



### <a name="developing-applications-to-run-inside-enclaves"></a>开发要在 enclaves 内运行的应用程序
开发应用程序时，可以使用[软件工具](application-development.md)来屏蔽领地内部的代码和数据部分。 这些工具确保受信任环境外部的任何人都不能查看或修改你的代码和数据。 

## <a name="next-steps"></a>后续步骤
- 阅读有关在 Azure 机密计算虚拟机上部署解决方案的[最佳实践](virtual-machine-solutions.md)。
- [部署 DCsv2-Series 虚拟机](quick-create-portal.md)
- 使用 OE SDK[开发 enclave 感知应用程序](application-development.md)