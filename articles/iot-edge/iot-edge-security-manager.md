---
title: 了解安全管理器如何保护设备和软件 - Azure IoT Edge | Microsoft Docs
description: 管理 IoT Edge 设备安全性情况和安全服务的完整性。
services: iot-edge
keywords: 安全性, 安全元素, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457533"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全管理器

Azure IoT Edge 安全管理器是一种界限明确的安全内核，用于通过抽象化安全硅硬件来保护 IoT Edge 设备及其所有组件。 It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Azure IoT Edge 安全管理器](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全管理器旨在保护 IoT Edge 设备和所有固有软件操作的完整性。 The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  IoT Edge 安全管理器是与安全硅硬件（如果可用）结合使用的软件，以帮助提供最高安全保证。  

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

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>设计原理

IoT Edge 安全守护程序遵循两个核心原则：最大化操作完整性，并最大程度地减小膨胀和改动。

#### <a name="maximize-operational-integrity"></a>最大程度地提高操作完整性

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. 通过适当集成，硬件信任根以静态方式度量并监视安全守护程序，并在运行时抵御篡改。

物理访问对 IoT 设备来说始终是一种威胁。 硬件信任根对于保护 IoT Edge 安全守护程序的完整性起重要作用。  Hardware root of trust come in two varieties:

* 用于保护敏感信息（如机密和加密密钥）的安全元素。
* 用于保护机密（如密钥）和敏感工作负载（如计量和计费）的安全 enclave。

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>最大程度地减小膨胀和改动

IoT Edge 安全守护程序的另一个核心原则是最大程度地减小改动。  对于最高的信任级别，IoT Edge 安全守护程序可与设备硬件信任根紧密结合，并作为本机代码运行。  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  示例会展示如何跳过更新以最大程度提高操作可用性，或阐述硬件信任根受到过多限制而无法处理大型更新有效负载。  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全守护程序的体系结构

![Azure IoT Edge 安全守护程序](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>云接口

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>管理 API

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. 这些注册将模块的标识映射到该模块的某些属性。 这些属性的几个示例是容器中运行的进程的进程标识符 (pid) 或 docker 容器内容的哈希。

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  在 IoT Edge 安全守护程序启动，并启动 IoT Edge 代理后，它可在证明 IoT Edge 代理未篡改后为 IoT Edge 代理创建隐式注册。 The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>容器 API

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>工作负载 API

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. 信任捆绑包包含模块应信任的所有其他服务器的 CA 证书。

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. 如果成功，则其使用注册的属性来度量该模块。 If the result of the measurement process matches the registration, a new proof of identity is generated. 相应 CA 证书（信任捆绑包）也返回到模块。  该模块使用此证书来连接到 IoT 中心、其他模块或启动服务器。 When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>集成和维护

Microsoft 维护 [GitHub 上 IoT Edge 安全守护程序](https://github.com/Azure/iotedge/tree/master/edgelet)的主要代码库。

#### <a name="installation-and-updates"></a>安装和更新

IoT Edge 安全守护程序的安装和更新通过操作系统的程序包管理系统进行管理。 具有硬件信任根的 IoT Edge 设备应通过安全启动和更新管理系统来管理守护程序的生命周期，从而进一步增强其完整性。 Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>版本控制

IoT Edge 运行时可跟踪和报告 IoT Edge 安全守护程序的版本。 该版本报告为 IoT Edge 代理模块报告的属性的 runtime.platform.version 特性。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬件安全模块平台抽象层 (HSM PAL)

HSM PAL 将所有信任硬件根抽象化，从而使 IoT Edge 开发者或用户从其复杂性中解脱出来。  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  HSM PAL 的实际实现取决于使用的特定安全硬件。 它的存在允许使用几乎任何安全硅硬件。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全硅硬件信任根

安全硅必须在 IoT Edge 设备硬件内定位标记信任。  安全硅种类繁多，包括受信任的平台模块 (TPM)、嵌入式安全元素 (eSE)、ARM TrustZone、Intel SGX 和自定义安全硅技术。  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全管理器集成和维护

IoT Edge 安全管理器旨在标识并隔离保护 Azure IoT Edge 平台的安全性和完整性的组件，以增强自定义。 第三方（如设备制造商）应使用其设备硬件提供可用的自定义安全功能。  有关如何在 Linux 和 Windows 平台上通过受信任的平台模块 (TPM) 增强 Azure IoT 安全管理器的信息，请访问“后续步骤”一节中的链接。 这些示例使用软件或虚拟 TPM，但直接应用于使用离散 TPM 设备。  

## <a name="next-steps"></a>后续步骤

阅读博客 [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)（保护智能边缘）。

[使用 Linux 虚拟机上的虚拟 TPM](how-to-auto-provision-simulated-device-linux.md) 创建和预配 IoT Edge 设备。

在 Windows 上使用模拟的 TPM 创建和预配 [IoT Edge 设备](how-to-auto-provision-simulated-device-windows.md)。
