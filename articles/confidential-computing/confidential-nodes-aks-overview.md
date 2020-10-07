---
title: Azure Kubernetes 服务 (AKS) 上的机密计算节点（公共预览版）
description: AKS 上的机密计算节点
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: ae3090689f9999c9ea6aa65447dadbdd7b0b2026
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90998306"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Azure Kubernetes 服务 (AKS) 上的机密计算节点（公共预览版）

使用敏感数据时，可使用 [Azure 机密计算](overview.md)保护敏感数据。 底层基础结构保护此数据不受其他应用程序、管理员和云提供商影响。 

## <a name="overview"></a>概述

Azure Kubernetes 服务 (AKS) 支持在 Intel SGX 上添加 [DCsv2 机密计算节点](confidential-computing-enclaves.md)。 这些节点通过允许用户级代码分配内存的专用区域，在基于硬件的可信执行环境 (TEE) 内运行敏感工作负载。 这些专用内存区域称为 enclave。 Enclave 旨在保护代码和数据不受以更高特权运行的进程的影响。 SGX 执行模型删除来宾 OS 和虚拟机监控程序的中间层。 这使你可以直接在 CPU 上执行容器应用程序，同时对特殊内存块进行加密。 


![sgx 节点概述](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS 机密节点功能

- 基于硬件的进程级容器可通过 SGX 可信执行环境 (TEE) 实现隔离 
- 异源节点池群集（混合机密和非机密节点池）
- 基于加密页面缓存 (EPC) 内存的 pod 计划
- 预安装 SGX DCAP 驱动程序
- 预安装 Intel FSGS 修补程序
- 支持基于 CPU 消耗的水平 pod 自动缩放和群集自动缩放
- 通过 AKS DaemonSet 的进程外证明帮助程序
- 通过 Ubuntu 18.04 Gen 2 VM 工作器节点的 Linux 容器支持

## <a name="aks-provided-daemon-sets"></a>AKS 提供的守护程序集

#### <a name="sgx-device-plugin"></a>SGX 设备插件 <a id="sgx-plugin"></a>

SGX 设备插件实现了用于 EPC 内存的 Kubernetes 设备插件接口。 实际上，此插件会使 EPC 内存成为 Kubernetes 中的额外资源类型。 用户可以指定此资源的限制，就像指定其他资源一样。 除了计划函数，设备插件还有助于为机密工作负载容器分配 SGX 设备驱动程序权限。 [此处](https://github.com/azure-samples/confidential-computing/blob/main/containersamples/helloworld/helloworld.yaml)有基于 EPC 内存的部署 (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) 的示例实现

#### <a name="sgx-quote-helper-service"></a>SGX 引用帮助程序服务 <a id="sgx-quote"></a>

执行远程证明的 enclave 应用程序需要生成 QUOTE。 此 QUOTE 提供应用程序的标识和状态的加密证明，以及 enclave 正在其中运行的环境。 QUOTE 生成依赖于英特尔提供的某些可信软件组件，这些组件属于 SGX 平台软件组件 (PSW/DCAP)。 此 PSW 打包为一个守护程序集，可在每个节点运行。 从 enclave 应用请求证明 QUOTE 时，可以利用它。 使用 AKS 提供的服务有助于更好地维护 PSW 与主机中其他 SW 组件之间的兼容性。 [详细了解](confidential-nodes-out-of-proc-attestation.md)它的使用情况和功能详细信息。

## <a name="programming--application-models"></a>编程和应用模型

### <a name="confidential-containers"></a>机密容器

[机密容器](confidential-containers.md)运行现有程序和最常用的编程语言运行时（Python、Node、Java 等）以及它们的现有库依赖项，而无需修改或重新编译任何源代码。 通过开放源代码项目和 Azure 合作伙伴，此模型是最快实现机密性的模型。 已准备好在安全 enclave 中运行的容器映像称为机密容器。

### <a name="enclave-aware-containers"></a>Enclave 感知容器

AKS 支持以下应用程序：编程为在机密节点上运行，并利用 SDK 和框架提供的特殊指令集。 此应用程序模型以最低的可信计算基础 (TCB) 为应用程序提供最大程度的控制。 [详细了解](enclave-aware-containers.md) enclave 感知容器。

## <a name="next-steps"></a>后续步骤

[使用机密计算节点部署 AKS 群集](./confidential-nodes-aks-get-started.md)

[机密容器快速入门示例](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 列表](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions