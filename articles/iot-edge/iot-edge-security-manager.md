---
title: Azure IoT Edge 安全管理器-Azure IoT Edge
description: 管理 IoT Edge 设备安全性情况和安全服务的完整性。
services: iot-edge
keywords: 安全性, 安全元素, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548690"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全管理器

Azure IoT Edge 安全管理器是一种界限明确的安全内核，用于通过抽象化安全硅硬件来保护 IoT Edge 设备及其所有组件。 安全管理器是安全强化的重点，并向原始设备制造商（OEM）提供技术集成点。

![Azure IoT Edge 安全管理器](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全管理器旨在保护 IoT Edge 设备和所有固有软件操作的完整性。 安全管理器从可信硬件（如果有）的基础硬件根转换到启动 IoT Edge 运行时并监视正在进行的操作。  IoT Edge 安全管理器是与安全硅硬件（如果可用）结合使用的软件，以帮助提供最高安全保证。  

IoT Edge 安全管理器的职责包括但不限于：

* 安全、慎重地启动 Azure IoT Edge 设备。
* 预配设备标识和转换信任（如果适用）。
* 托管和保护云服务（如设备预配服务）的设备组件。
* 使用唯一标识安全预配 IoT Edge 模块。
* 通过公证服务作为设备硬件信任根的网关守卫。
* 监视运行时 IoT Edge 操作的完整性。

IoT Edge 安全管理器包含三个组件：

* IoT Edge 安全守护程序。
* 硬件安全模块平台抽象层 (HSM PAL)。
* 可选但强烈推荐的硬件硅信任根或 HSM。

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 安全守护程序

IoT Edge 安全守护程序负责 IoT Edge 安全管理器的逻辑操作。 它代表 IoT Edge 设备的受信任计算基础的重要部分。

### <a name="design-principles"></a>设计原理

IoT Edge 安全守护程序遵循两个核心原则：最大化操作完整性，并最大程度地减小膨胀和改动。

#### <a name="maximize-operational-integrity"></a>最大程度地提高操作完整性

IoT Edge 安全守护程序在受信任硬件的任何给定根的防御能力内最大限度地操作。 通过适当集成，硬件信任根以静态方式度量并监视安全守护程序，并在运行时抵御篡改。

物理访问对 IoT 设备来说始终是一种威胁。 硬件信任根对于保护 IoT Edge 安全守护程序的完整性起重要作用。  信任的硬件根分为两种：

* 用于保护敏感信息（如机密和加密密钥）的安全元素。
* 用于保护机密（如密钥）和敏感工作负载（如计量和计费）的安全 enclave。

存在两种使用信任的硬件根的执行环境：

* 标准或丰富的执行环境（REE），它依赖于安全元素的使用来保护敏感信息。
* 受信任的执行环境（t），它依赖于使用 secure enclave 技术来保护敏感信息并为软件执行提供保护。

对于使用 secure enclaves 作为信任的硬件根的设备，IoT Edge 安全守护程序内的敏感逻辑应位于 enclave 中。  安全守护程序的不敏感部分可以在该外。  在任何情况下，原始设计制造商（ODM）和原始设备制造商（OEM）应该从其 HSM 扩展信任，以便在启动和运行时对 IoT Edge 安全守护程序的完整性进行度量和保护。

#### <a name="minimize-bloat-and-churn"></a>最大程度地减小膨胀和改动

IoT Edge 安全守护程序的另一个核心原则是最大程度地减小改动。  对于最高的信任级别，IoT Edge 安全守护程序可与设备硬件信任根紧密结合，并作为本机代码运行。  这种类型的实现通常是通过信任的安全更新路径（而不是 OS 提供的更新机制）的硬件根来更新 daemon 软件的，这在某些情况下可能会有挑战性。  尽管建议将安全续订用于 IoT 设备，但过多的更新要求或大型更新负载可以通过多种方式扩展威胁面。  示例会展示如何跳过更新以最大程度提高操作可用性，或阐述硬件信任根受到过多限制而无法处理大型更新有效负载。  在这种情况下，IoT Edge 安全守护程序的设计很简单，只需减小空间，并将受信任的计算基础降到最低，从而最大程度地减少

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全守护程序的体系结构

![Azure IoT Edge 安全守护程序](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 安全守护程序利用信任技术的任何可用硬件根来增强安全性。  当硬件技术提供受信任的执行环境时，它还允许在标准/丰富执行环境（REE）与受信任的执行环境（t）之间进行拆分世界操作。 特定于角色的接口允许 IoT Edge 的主要组件确保 IoT Edge 设备及其操作的完整性。

#### <a name="cloud-interface"></a>云接口

云接口允许 IoT Edge 安全守护程序访问云服务（如云补充），以访问安全更新等设备安全。  例如，IoT Edge security daemon 目前使用此接口来访问 Azure IoT 中心[设备预配服务](https://docs.microsoft.com/azure/iot-dps/)，以实现设备标识生命周期管理。  

#### <a name="management-api"></a>管理 API

IoT Edge security daemon 提供管理 API，该 API 在创建/启动/停止/删除 IoT Edge 模块时由 IoT Edge 代理调用。 安全守护程序为所有活动模块存储 "注册"。 这些注册将模块的标识映射到该模块的某些属性。 例如，这些模块属性包括在容器中运行的进程的进程标识符（pid）以及 docker 容器内容的哈希。

工作负荷 API 使用这些属性（如下所述）来验证调用方是否已获得操作授权。

管理 API 是一个特权 API，只能从 IoT Edge 代理调用。  由于 IoT Edge security daemon 引导并启动 IoT Edge 代理，因此它将验证 IoT Edge 代理是否未被篡改，然后可以为 IoT Edge 代理创建隐式注册。 工作负荷 API 使用的同一证明过程也会将对管理 API 的访问限制为仅限 IoT Edge 代理。

#### <a name="container-api"></a>容器 API

容器 API 与用于模块管理的容器系统交互，如小鲸鱼或 Docker。

#### <a name="workload-api"></a>工作负载 API

工作负荷 API 可供所有模块访问。 它提供标识证明（作为 HSM 根签名令牌或 X509 证书）以及相应的信任捆绑到模块。 信任捆绑包包含模块应信任的所有其他服务器的 CA 证书。

IoT Edge security daemon 使用证明进程来保护此 API。 当模块调用此 API 时，安全守护程序会尝试查找该标识的注册。 如果成功，则其使用注册的属性来度量该模块。 如果度量过程的结果与注册匹配，则会生成新的标识证明。 相应 CA 证书（信任捆绑包）也返回到模块。  该模块使用此证书来连接到 IoT 中心、其他模块或启动服务器。 当已签名的令牌或证书过期时，模块负责请求新证书。

### <a name="integration-and-maintenance"></a>集成和维护

Microsoft 维护 [GitHub 上的 IoT Edge 安全守护程序](https://github.com/Azure/iotedge/tree/master/edgelet)的主要代码库。

#### <a name="installation-and-updates"></a>安装和更新

IoT Edge 安全守护程序的安装和更新通过操作系统的程序包管理系统进行管理。 具有硬件信任根的 IoT Edge 设备应通过安全启动和更新管理系统来管理守护程序的生命周期，从而进一步增强其完整性。 设备制造商应根据各自的设备功能，浏览这些途径。

#### <a name="versioning"></a>版本控制

IoT Edge 运行时可跟踪和报告 IoT Edge 安全守护程序的版本。 该版本报告为 IoT Edge 代理模块报告的属性的 runtime.platform.version 特性。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬件安全模块平台抽象层 (HSM PAL)

HSM PAL 将所有硬件信任根抽象化，从而使 IoT Edge 开发者或用户从其复杂性中解脱出来。  它包括应用程序编程接口（API）和事务域通信过程的组合，例如标准执行环境和安全 enclave 之间的通信。  HSM PAL 的实际实现取决于使用的特定安全硬件。 它的存在允许使用几乎任何安全硅硬件。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全硅硬件信任根

安全硅必须在 IoT Edge 设备硬件内定位标记信任。  安全硅种类繁多，包括受信任的平台模块 (TPM)、嵌入式安全元素 (eSE)、ARM TrustZone、Intel SGX 和自定义安全硅技术。  建议在设备中使用安全硅信任，因为与 IoT 设备的物理可访问性相关的威胁。

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全管理器集成和维护

IoT Edge 安全管理器旨在标识并隔离保护 Azure IoT Edge 平台的安全性和完整性的组件，以增强自定义。 第三方（如设备制造商）应使用其设备硬件提供可用的自定义安全功能。  有关如何在 Linux 和 Windows 平台上通过受信任的平台模块 (TPM) 增强 Azure IoT 安全管理器的信息，请访问“后续步骤”一节中的链接。 这些示例使用软件或虚拟 TPM，但直接应用于使用离散 TPM 设备。  

## <a name="next-steps"></a>后续步骤

阅读博客 [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)（保护智能边缘）。

[使用 Linux 虚拟机上的虚拟 TPM](how-to-auto-provision-simulated-device-linux.md) 创建和预配 IoT Edge 设备。

在 Windows 上使用模拟的 TPM 创建和预配 [IoT Edge 设备](how-to-auto-provision-simulated-device-windows.md)。
