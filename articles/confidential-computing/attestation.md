---
title: Azure 中的证明 enclaves
description: 了解如何使用证明来验证机密计算受信任环境是否安全
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994848"
---
# <a name="attesting-sgx-enclaves"></a>证明 SGX Enclaves

Azure 上的机密计算提供基于 Intel SGX 的虚拟机，可隔离部分代码或数据。 使用这些 [enclaves](confidential-computing-enclaves.md)时，需要获取验证和验证受信任环境的安全性。 这种验证是一个证明过程。 

## <a name="overview"></a>概述 

通过证明，信赖方可以更加确信其软件：(1) 在领地中运行；(2) 领地是最新且安全的。 例如，领地请求底层硬件生成一个凭据，该凭据可以证明该领地在平台上存在。 然后，可以向另一个领地提供报告，验证该报告是否是在同一平台上生成的。

![enclave 中的证明代码](media/attestation/attestation.png)



必须使用与系统软件和芯片兼容的安全证明服务来实施证明。 可使用的服务示例包括

- [Microsoft Azure 认证 (预览版) ](https://docs.microsoft.com/azure/attestation/overview) 或
- [Intel 的证明和预配服务](https://software.intel.com/sgx/attestation-services)


这两者都与 Azure 机密计算兼容 Intel SGX 基础结构。 

## <a name="next-steps"></a>后续步骤
尝试 [Microsoft Azure 证明示例 enclave 感知应用](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)。