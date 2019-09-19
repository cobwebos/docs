---
title: Azure IoT 的安全建议 |Microsoft Docs
description: 本文总结了额外的步骤，以确保 Azure IoT 中心解决方案的安全性。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 09/19/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 7253ec04efe914f68e9798fca49233f11c5102ef
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129795"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物联网（IoT）部署的安全建议

本文包含适用于 Azure 应用服务的安全建议。 实施这些建议将有助于你履行我们的共享职责模型中描述的安全职责，并改进 Web 应用解决方案的总体安全性。 若要详细了解 Microsoft 如何满足服务提供商的责任，请阅读[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)。

Azure 安全中心会自动监视本文中包含的一些建议。 Azure 安全中心是保护 Azure 中资源的第一道防线。 它会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向您提供有关如何解决这些问题的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅[Azure 安全中心中的安全建议](../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-intro.md)

## <a name="recommendations"></a>建议

| 类别 | 建议 | 注释 | 由 ASC 支持 |
|-|-|----|--|
| 常规 | 保持最新状态 | 使用最新版的受支持平台、编程语言、协议和框架。 | - |
| 常规 | 保持身份验证密钥安全 | 部署后，请确保设备 Id 及其身份验证密钥的物理安全。 这将避免恶意设备伪装为注册设备。 | - |
| 常规 | 尽可能使用设备 Sdk | 设备 Sdk 实现多种安全功能（如、加密、身份验证等），以帮助你开发强大的安全设备应用程序。 有关详细信息，请参阅[了解和使用 Azure IoT 中心 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 。 | - |
| 标识和访问管理 | 定义中心的访问控制 | [了解并定义](iot-security-deployment.md#securing-the-cloud)每个组件在 IoT 中心解决方案中将具有的访问类型（基于功能）。 允许的权限包括*注册表读取*、 *RegistryReadWrite*、 *ServiceConnect*和*DeviceConnect*。 [IoT 中心中的默认共享访问策略](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)还有助于根据组件的角色定义每个组件的权限。 | - |
| 标识和访问管理 | 定义后端服务的访问控制 | IoT 中心解决方案引入的数据可供其他 Azure 服务（例如[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)、[流分析](https://docs.microsoft.com/azure/stream-analytics/)、[应用服务](https://docs.microsoft.com/azure/app-service/)、[逻辑应用](https://docs.microsoft.com/azure/logic-apps/)和[Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)）使用。 对于这些服务，请务必了解并允许相应的访问权限。 | - |
| 数据保护 | 安全设备身份验证 | 使用[唯一的标识密钥或安全令牌](iot-security-deployment.md#iot-hub-security-tokens)或每个设备的[设备 x.509 证书](iot-security-deployment.md#x509-certificate-based-device-authentication)，确保设备与 IoT 中心之间的通信安全。 使用适当的方法，根据所[选的协议（MQTT、AMQP 或 HTTPS）使用安全令牌](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 | - |
| 数据保护 | 安全设备通信 | IoT 中心使用传输层安全性（TLS）标准来保护与设备的连接，支持1.2 和1.0 版本。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)确保最大安全性。 | - |
| 数据保护 | 保护服务通信 | IoT 中心提供终结点，以便仅使用 TLS 协议连接到后端服务（如[Azure 存储](/azure/storage/)或[事件中心](/azure/event-hubs)），并且不会在未加密的通道上公开任何终结点。 一旦此数据达到这些后端服务进行存储或分析，请确保为该服务使用适当的安全和加密方法，并在后端保护敏感信息。 | - |
| 网络 | 保护对设备的访问 | 将设备中的硬件端口保持在最低限度，以避免不需要的访问。 此外，还构建了阻止或检测设备物理篡改的机制。 有关详细信息，请参阅[IoT 安全最佳实践](iot-security-best-practices.md)。 | - |
| 网络 | 构建安全的硬件 | 合并了安全功能（如加密存储）或受信任的平台模块（TPM），以使设备和基础结构更安全。 让设备操作系统和驱动程序升级到最新版本，如果空间允许，请安装防病毒和反恶意软件功能。 阅读[IoT 安全体系结构](iot-security-architecture.md)，了解这可以如何帮助减轻几个安全威胁。 | - |
| 监视 | 监视对你的设备的未经授权的访问 |  使用设备操作系统的日志记录功能来监视设备或其端口的任何安全漏洞或物理篡改。 | - |
| 监视 | 从云中监视 IoT 解决方案 | 使用[Azure Monitor 中的度量值](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)监视 IoT 中心解决方案的总体运行状况。 | - |
| 监视 | 设置诊断 | 通过在解决方案中记录事件，然后将诊断日志发送到 Azure Monitor 来密切监视操作，以了解性能。 有关详细信息，请阅读[监视和诊断 IoT 中心中的问题](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)。 | - |

## <a name="next-steps"></a>后续步骤

对于涉及 Azure IoT 的高级方案，你可能需要考虑其他安全要求。 有关更多指导，请参阅[IoT 安全体系结构](iot-security-architecture.md)。

