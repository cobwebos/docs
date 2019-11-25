---
title: 安全框架 - Azure IoT Edge | Microsoft Docs
description: 了解用于开发 Azure IoT Edge 的安全标准、身份验证标准和授权标准，在设计解决方案时应考虑这些标准
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0d315c7955fff854fc52f73de16eda71b9296b40
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452450"
---
# <a name="security-standards-for-azure-iot-edge"></a>Azure IoT Edge 的安全标准

Azure IoT Edge addresses the risks that are inherent when moving your data and analytics to the intelligent edge. The IoT Edge security standards balance flexibility for different deployment scenarios with the protection that you expect from all Azure services. 

IoT Edge runs on various makes and models of hardware, supports several operating systems, and applies to diverse deployment scenarios. The risk of a deployment scenario depends on factors that include solution ownership, deployment geography, data sensitivity, privacy, application vertical, and regulatory requirements. Rather than offering concrete solutions for specific scenarios, IoT Edge is an extensible security framework based on well-grounded principles that are designed for scale. 
 
本文提供 IoT Edge 安全框架的概述。 For more information, see [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>标准

标准使审查和实施更简单，两者是安全性的标志。 A security solution should lend itself to scrutiny under evaluation to build trust and shouldn't be a hurdle to deployment. The design of the framework to secure Azure IoT Edge is based on time-tested and industry proven security protocols for familiarity and reuse. 

## <a name="authentication"></a>Authentication

When you deploy an IoT solution, you need to know that only trusted actors, devices, and modules have access to your solution. Certificate-based authentication is the primary mechanism for authentication for the Azure IoT Edge platform. This mechanism is derived from a set of standards governing Public Key Infrastructure (PKiX) by the Internet Engineering Task Force (IETF).     

与 Azure IoT Edge 设备交互（无论是物理上的交互还是通过网络连接交互）的所有设备、模块和执行组件都应该有唯一的证书标识。 Not every scenario or component may lend itself to certificate-based authentication, so the extensibility of the security framework offers secure alternatives. 

For more information, see [Azure IoT Edge certificate usage](iot-edge-certs.md).

## <a name="authorization"></a>授权

最低特权原则指出，系统的用户和组件应该只有权访问其执行角色所要访问的最少量资源和数据。 设备、模块和执行组件应该只能在架构方面允许时，访问其权限范围内的资源和数据。 Some permissions are configurable with sufficient privileges and others are architecturally enforced.  For example, some modules may be authorized to connect to Azure IoT Hub. However, there is no reason why a module in one IoT Edge device should access the twin of a module in another IoT Edge device.

Other authorization schemes include certificate signing rights and role-based access control (RBAC). 

## <a name="attestation"></a>证明

Attestation ensures the integrity of software bits, which is important for detecting and preventing malware.  The Azure IoT Edge security framework classifies attestation under three main categories:

* 静态证明
* 运行时证明
* 软件证明

### <a name="static-attestation"></a>静态证明

Static attestation verifies the integrity of all software on a device during power-up, including the operating system, all runtimes, and configuration information. Because static attestation occurs during power-up, it's often referred to as secure boot. The security framework for IoT Edge devices extends to manufacturers and incorporates secure hardware capabilities that assure static attestation processes. These processes include secure boot and secure firmware upgrade.  Working in close collaboration with silicon vendors eliminates superfluous firmware layers, so minimizes the threat surface. 

### <a name="runtime-attestation"></a>运行时证明

Once a system has completed a secure boot process, well-designed systems should detect attempts to inject malware and take proper countermeasures. Malware attacks may target the system's ports and interfaces. If malicious actors have physical access to a device, they may tamper with the device itself or use side-channel attacks to gain access. Such malcontent, whether malware or unauthorized configuration changes, can't be detected by static attestation because it is injected after the boot process. 设备硬件提供或强制实施的对策有助于抵御这种威胁。  The security framework for IoT Edge explicitly calls for extensions that combat runtime threats.  

### <a name="software-attestation"></a>软件证明

All healthy systems, including intelligent edge systems, need patches and upgrades.  Security is important for update processes, otherwise they can be potential threat vectors.  The security framework for IoT Edge calls for updates through measured and signed packages to assure the integrity of and authenticate the source of the packages.  This standard applies to all operating systems and application software bits. 

## <a name="hardware-root-of-trust"></a>硬件信任根

For many intelligent edge devices, especially devices that can be physically accessed by potential malicious actors, hardware security is the last defense for protection. Tamper resistant hardware is crucial for such deployments. Azure IoT Edge encourages secure silicon hardware vendors to offer different flavors of hardware root of trust to accommodate various risk profiles and deployment scenarios. 硬件信任可能来源于一般安全协议标准，例如受信任的平台模块 (ISO/IEC 11889) 和受信任的计算组的设备身份合成引擎 (DICE)。 Secure enclave technologies like TrustZones and Software Guard Extensions (SGX) also provide hardware trust. 

## <a name="certification"></a>认证

To help customers make informed decisions when procuring Azure IoT Edge devices for their deployment, the IoT Edge framework includes certification requirements.  Foundational to these requirements are certifications pertaining to security claims and certifications pertaining to validation of the security implementation.  For example, a security claim certification means that the IoT Edge device uses secure hardware known to resist boot attacks. A validation certification means that the secure hardware was properly implemented to offer this value in the device.  In keeping with the principle of simplicity, the framework tries to keep the burden of certification minimal.   

## <a name="extensibility"></a>扩展性

With IoT technology driving different types of business transformations, security should evolve in parallel to address emerging scenarios.  The Azure IoT Edge security framework starts with a solid foundation on which it builds in extensibility into different dimensions to include: 

* 第一方安全服务，如 Azure IoT 中心的设备预配服务。
* Third-party services like managed security services for different application verticals (like industrial or healthcare) or technology focus (like security monitoring in mesh networks, or silicon hardware attestation services) through a rich network of partners.
* 旧系统，用于包括使用备用安全策略的更新，比如使用安全技术而不是证书进行身份验证和身份管理。
* 安全硬件，用于吸收新的安全硬件技术以及芯片合作伙伴的贡献。

In the end, securing the intelligent edge requires collaborative contributions from an open community driven by the common interest in securing IoT.  These contributions might be in the form of secure technologies or services.  The Azure IoT Edge security framework offers a solid foundation for security that is extensible for the maximum coverage to offer the same level of trust and integrity in the intelligent edge as with Azure cloud.  

## <a name="next-steps"></a>后续步骤

阅读有关 Azure IoT Edge 正在[保护智能边缘](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)的详细信息。
