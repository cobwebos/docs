---
title: Azure 上的 Enclave 感知容器
description: Azure Kubernetes 服务 (AKS) 上面向 Enclave 的应用程序容器支持
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b388b8efb7d993baa243975dbf499576246f3185
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998303"
---
# <a name="enclave-aware-containers"></a>Enclave 感知容器

Enclave 是受保护的内存区域，为数据和代码执行提供机密性。 它是受信任的执行环境 (TEE) 的一个实例，受硬件保护。 AKS 上的机密计算节点使用 [Intel Software Guard Extensions (SGX)](https://software.intel.com/sgx) 在每个容器应用程序之间的节点中创建独立的 enclave 环境。

就像 Intel SGX 虚拟机一样，为在 enclave 中运行而开发的容器应用程序具有两个组件：

- 不受信任的组件（称为宿主）和
- 受信任的组件（称为领地）。

![Enclave 感知容器体系结构](./media/enclave-aware-containers/enclaveawarecontainer.png)

Enclave 感知容器应用程序体系结构使你可以最大程度地控制实现，同时在 enclave 中保持较低的代码内存占用率。 最大程度减少 enclave 中运行的代码有助于减少攻击面。   

## <a name="enablers"></a>启用程序

### <a name="open-enclave-sdk"></a>开放式 Enclave SDK
开放式 Enclave SDK 是一个与硬件无关的开源库，用于开发利用基于硬件的受信任执行环境的 C、C++ 应用程序。 当前实现提供对 Intel SGX 的支持，以及对 [Arm TrustZone 上的 OP-TEE OS](https://optee.readthedocs.io/en/latest/general/about.html) 的预览支持。

从[此处](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)开始使用基于开放式 Enclave 的容器应用程序

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
Intel 维护着一个软件开发工具包，用于生成适用于 Linux 和 Windows 容器工作负载的 SGX 应用程序。 AKS 机密计算节点目前不支持 Windows 容器。

从[此处](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)开始使用基于 Intel SGX 的应用程序

### <a name="confidential-consortium-framework-ccf"></a>机密联盟框架 (CCF)
机密联盟框架 (CCF) 是一个开源框架，用于生成一种安全、高度可用、高性能的新应用程序，这些应用程序侧重于多方计算和数据。 CCF 可以实现大规模机密网络来满足关键企业需求，提供了一种方法来加速生产和促进企业采用基于联盟的区块链和多方计算技术。

从[此处](https://github.com/Microsoft/CCF)开始使用 Azure 机密计算和 CCF

### <a name="confidential-inferencing-onnx-runtime"></a>机密推理 ONNX 运行时

基于开源 enclave 的 ONNX 运行时在客户端和推理服务之间建立了一个安全通道，确保请求和响应都不会离开安全 enclave。 

此解决方案允许你引入经过 ML 训练的现有模型并机密地运行，同时通过证明和验证在客户端与服务器之间建立信任。 

从[此处](https://aka.ms/confidentialinference)开始将 ML 模型直接迁移到 ONNX 运行时

## <a name="container-samples-implementations"></a>容器示例实现

[AKS 上 enclave 感知容器的 Azure 示例](https://github.com/Azure-Samples/enclave-aware-container-samples)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
[Confidential Containers]: /confidential-computing/containercompute/confidential-containers