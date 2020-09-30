---
title: Azure 上 Intel SGX 引用帮助程序 DaemonSet 的进程外证明支持
description: 用于在 SGX 应用程序进程之外生成引用的 DaemonSet。 本文介绍如何为容器内运行的机密工作负载提供进程外证明设备。
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 90b74e51dce994c3041c15feec729ff1fae2bb00
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998304"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set"></a>具有 SGX 引用帮助程序守护程序集的平台软件管理

执行远程证明的 [enclave 应用程序](confidential-computing-enclaves.md)需要生成的 QUOTE。 此 QUOTE 提供应用程序的标识和状态的加密证明，以及 enclave 正在运行的环境。 生成 QUOTE 需要使用英特尔的平台软件组件 (PSW) 中的可信软件组件。

## <a name="overview"></a>概述
 
英特尔支持两种证明模式来运行引用生成：
- **进程内**：在 enclave 应用程序进程内托管可信软件组件

- **进程外**：在 enclave 应用程序外托管可信软件组件。
 
使用开放式 Enclave SDK 构建的 SGX 应用程序默认使用进程内证明模式。 基于 SGX 的应用程序使用进程外模式，并且需要额外托管和公开所需的组件，如应用程序外部的 Architectural Enclave Service Manager (AESM)。

强烈建议使用此功能，因为它在英特尔平台更新或 DCAP 驱动程序更新期间可增加 enclave 应用的正常运行时间。

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>为什么选择进程外模式，进程外模式有何优势？

-   每个容器化应用程序的 PSW 引用生成组件都无需更新：使用进程外模式，容器所有者不需要管理其容器内的更新。 而是依赖提供商提供的接口来调用容器外部的集中式服务，该容器将由提供商进行更新和管理。

-   无需担心由于 PSW 组件过期而导致证明失败：引用生成涉及可信 SW 组件 - Quoting Enclave (QE) 和 Provisioning Certificate Enclave (PCE)，这两个组件是可信计算基 (TCB) 的一部分。 这些 SW 组件必须保持最新，才能维护证明要求。 由于由提供商管理这些组件的更新，因此客户将不必处理由于其容器中过期的可信 SW 组件而导致的证明失败。

-   充分利用 EPC 内存 在进程内证明模式下，每个 enclave 应用程序都需要实例化 QE 和 PCE 的副本以实现远程证明。 使用进程外模式，容器无需托管这些 enclave，因此不会消耗容器配额中的 enclave 内存。

-   防止内核强制执行的安全措施 当 SGX 驱动程序被向上流式传输到 Linux 内核时，将强制 enclave 具有更高的特权。 此特权允许 enclave 调用 PCE，这将破坏在进程内模式下运行的 enclave 应用程序。 默认情况下，enclave 不会获取此权限。 向 enclave 应用程序授予此权限需要更改应用程序安装进程。 而进程外模型很容易处理此情况，因为处理进程外请求的服务提供商将确保通过此特权安装该服务。

-   无需检查与 PSW 和 DCAP 的后向兼容性。 提供商在更新 PSW 的引用生成组件之前会先验证这些组件的更新的后向兼容性。 这将有助于在部署机密工作负载的更新之前，提前处理兼容性问题并解决这些问题。

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>进程外证明模式如何在机密工作负载场景中运行？

高级设计遵循以下模式：引用请求程序和引用生成单独执行，但在同一物理计算机上执行。 引用生成将集中完成，并服务于来自所有实体的 QUOTE 请求。 需要正确定义接口并确保其可被请求引用的任何实体发现。

![SGX 引用帮助程序 aesm](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

上述抽象模型利用现有的 AESM 服务，适用于机密工作负载场景。 AESM 在 Kubernetes 群集中作为 DaemonSet 进行容器化和部署。 Kubernetes 保证 AESM 服务容器的单个实例（包装在 Pod 中）部署到每个代理节点上。 新的 SGX Quote DaemonSet 将依赖于 sgx-device-plugin daemonset，因为 AESM 服务容器将从 sgx-device-plugin 请求 EPC 内存以启动 QE 和 PCE enclave。

每个容器都需要在创建过程中设置环境变量 SGX_AESM_ADDR=1 才能选择使用进程外引用生成。 容器还应包括包 libsgx-quote-ex，用于将请求引导到默认 Unix 域套接字

应用程序仍可像以前一样使用进程内证明，但应用程序中不能同时使用进程内和进程外。 进程外基础结构默认可用并消耗资源。

## <a name="sample-implementation"></a>示例实现

下面的 docker 文件是基于 Open Enclave 的应用程序的示例。 在 docker 文件中设置 SGX_AESM_ADDR=1 环境变量，或在部署文件中设置它。 请按照下面的示例了解 docker 文件和部署 yaml 详细信息。 

  > [!Note] 
  > 需要将英特尔提供的 libsgx-quote-ex 打包在应用程序容器中，以使进程外证明正常运行。
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
也可在部署 yaml 文件中设置进程外证明模式，如下所示

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>后续步骤
[在 AKS 上预配机密节点（DCsv2 系列）](./confidential-nodes-aks-get-started.md)

[机密容器快速入门示例](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 列表](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions