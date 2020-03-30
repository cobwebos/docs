---
title: Azure 红帽开放移位的支持资源
description: 了解 Microsoft Azure 红帽 OpenShift 支持哪些 Azure 区域和虚拟机大小。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243661"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure 红帽开放移位资源

本主题列出了 Microsoft Azure 红帽 OpenShift 服务支持的 Azure 区域和虚拟机大小。

## <a name="azure-regions"></a>Azure 区域

有关可以部署 Azure 红帽开放Shift 群集的区域当前列表，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

## <a name="virtual-machine-sizes"></a>虚拟机大小

以下是可以为 Azure 红帽 OpenShift 群集中的计算节点指定的受支持的虚拟机大小。

> [!Important]
> 每个 VM 都有不同数量的驱动器可以连接。 这可能不能像内存或 CPU 大小那样立即清晰。
> 并非所有 VM 大小都可在所有区域中使用。 即使 API 支持您指定的大小，如果大小在指定的区域中不可用，也可能收到错误。
> 有关详细信息[，请参阅每个区域支持的 VM 大小的当前列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="compute-node-sizes"></a>计算节点大小

Azure 红帽 OpenShift REST API 支持以下计算节点大小：

|大小|vCPU|RAM|
|-|-|-|
|标准 D4s v3|4|16 GB|
|标准 D8s v3|8|32 GB|
|标准 D16s v3|16|64 GB|
|标准 D32s v3|32|128 GB|
|-|-|-|
|标准 E4s v3|4|32 GB|
|标准 E8s v3|8|64 GB|
|标准 E16s v3|16|128 GB|
|标准 E32s v3|32|256 GB|
|-|-|-|
|标准 F8s v2|8|16 GB|
|标准 F16s v2|16|32 GB|
|标准 F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>主节点大小

Azure 红帽 OpenShift REST API 支持以下主节点/基础结构节点大小：

|大小|vCPU|RAM|
|-|-|-|
|标准 D4s v3|4|16 GB|
|标准 D8s v3|8|32 GB|
|标准 D16s v3|16|64 GB|
|标准 D32s v3|32|128 GB|

## <a name="next-steps"></a>后续步骤

尝试[创建 Azure 红帽 OpenShift 群集](tutorial-create-cluster.md)教程。
