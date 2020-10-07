---
title: Azure 机密计算概述
description: Azure 机密计算 (ACC) 的概述
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 16f45c39a329998f4b4da4ea89315683a0fab790
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90967580"
---
# <a name="confidential-computing-on-azure"></a>Azure 上的机密计算

在云中处理敏感数据时，可以使用 Azure 机密计算将这些数据隔离。 许多行业都使用机密计算来保护其数据，方法是通过使用机密计算来：

- 保护金融数据
- 保护患者信息
- 对敏感信息运行机器学习过程
- 针对来自多个源的加密数据集执行算法


## <a name="overview"></a>概述
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

我们知道，保护云数据非常重要。 我们将会倾听你的忧虑。 下面只是客户在将敏感工作负荷转移到云时可能会提出的一部分问题： 

- 如何确保 Microsoft 无法访问未加密的数据？
- 如何防止公司内部的特权管理员造成安全威胁？
- 还有哪些方法可以防止第三方访问敏感的客户数据？

Microsoft Azure 可帮助你最小化受攻击面，以增强数据保护。 Azure 已提供了许多工具来通过客户端加密和服务器端加密等模型保护[静态数据](../security/fundamentals/encryption-atrest.md)。 此外，Azure 还提供了通过安全协议（例如 TLS 和 HTTPS）加密[传输中数据](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)的机制。 本页介绍了数据加密的第三个支柱 - 使用中数据的加密。

## <a name="introduction-to-confidential-computing"></a>机密计算简介  

机密计算是由[机密计算联盟](https://confidentialcomputing.io/) (CCC) 定义的一个行业术语。CCC 是专业定义机密计算并加速其采用的基金会。 CCC 定义机密计算，如下所示：通过在基于硬件的受信任执行环境 (TEE) 中执行计算来保护使用中的数据。

TEE 是强制仅执行已授权代码的环境。 TEE 外部的任何代码都无法读取或篡改该环境中的任何数据。 

### <a name="lessen-the-need-for-trust"></a>减少对信任的需求
在云中运行工作负载要求信任。 将此信任授予各种提供程序，以启用应用程序的不同组件。


**应用软件供应商**：通过本地部署，使用开放源代码，或者通过构建内部应用程序软件来信任软件。

**硬件供应商**：通过使用本地硬件或内部硬件来信任硬件。 

**基础结构提供商**：信任云提供商或管理你自己的本地数据中心。


利用 Azure 机密计算，可通过降低对计算云基础结构各个方面的信任需求，更轻松地信任云提供商。 Azure 机密计算可最大程度地降低对主机 OS 内核、虚拟机监控程序、VM 管理员和主机管理员的信任需求。

### <a name="reducing-the-attack-surface"></a>缩小攻击面
可信计算基 (TCB) 指的是提供安全环境的系统的所有硬件、固件和软件组件。 TCB 内的组件被视为“关键”组件。 如果 TCB 内部有一个组件存在风险，则可能危及整个系统的安全性。 

更低的 TCB 意味着更高的安全性。 受各种漏洞、恶意软件、攻击和恶意用户的影响的风险更小。 Azure 机密计算旨在通过提供 TEE 来降低云工作负载的 TCB。 TEE 将 TCB 简化为受信任的运行时二进制文件、代码和库。 使用 Azure 基础结构和服务进行机密计算时，可以从 TCB 中删除所有的 Microsoft 内容。


## <a name="using-azure-for-cloud-based-confidential-computing"></a>使用 Azure 进行基于云的机密计算 <a id="cc-on-azure"></a>

借助 Azure 机密计算，可以在虚拟化环境中利用机密计算功能。 现在可以使用工具、软件和云基础结构在安全硬件的基础上生成解决方案。  

**阻止未经授权的访问**：在云中运行敏感数据。 信任 Azure 可以提供可能的最好数据保护，且对目前已完成的工作几乎不产生影响。

**法规遵从**：迁移到云，并保持对数据的完全控制，以满足政府关于个人信息和组织 IP 保护的法规。

**安全且不受信任的协作**：通过跨组织，甚至跨竞争对手合并数据，获得广泛的数据分析信息和更深入的见解，从而解决行业范围的工作规模问题。

**隔离处理**：提供一批新产品，可利用盲处理消除对私人数据的责任。 服务提供商甚至无法检索用户数据。 

## <a name="get-started"></a>入门
### <a name="azure-compute"></a>Azure 计算
在 Azure 的机密计算 IaaS 产品基础之上构建应用程序。
- 虚拟机 (VM)：[DCsv2 系列](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS)：[协调机密容器](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure 安全性 
通过验证方法和硬件绑定密钥管理来确保工作负载的安全性。 
- 证明：[Microsoft Azure 证明（预览版）](https://docs.microsoft.com/azure/attestation/overview)
- 密钥管理：托管 HSM（预览版）

### <a name="develop"></a>开发
开始使用开发 Enclave 感知应用程序，并使用机密推理框架部署机密算法。
- 编写要在 DCsv2 VM 上运行的应用程序：[开放式 Enclave SDK](https://github.com/openenclave/openenclave)
- ONNX 运行时中的机密 ML 模型：[机密推理 (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>后续步骤

部署 DCsv2 系列虚拟机并在其上安装 OE SDK。

> [!div class="nextstepaction"]
> [在 Azure 市场中部署机密计算 VM](quick-create-marketplace.md)
