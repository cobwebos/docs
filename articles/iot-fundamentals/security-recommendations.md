---
title: Azure IoT 的安全建议 |微软文档
description: 本文总结了确保 Azure IoT 中心解决方案中安全性的其他步骤。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728982"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物联网 （IoT） 部署的安全建议

本文包含 IoT 的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 它定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-intro.md)

## <a name="general"></a>常规

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 保持最新状态 | 使用最新版的受支持平台、编程语言、协议和框架。 | - |
| 保护身份验证密钥的安全 | 部署后，请保持设备 I 及其身份验证密钥的物理安全。 这将避免伪装成注册设备的恶意设备。 | - |
| 尽可能使用设备 SDK | 设备 SDK 实现各种安全功能，如加密、身份验证等，以帮助您开发强大而安全的设备应用程序。 有关详细信息[，请参阅了解和使用 Azure IoT 中心 SDK。](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) | - |

## <a name="identity-and-access-management"></a>标识和访问管理 

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 定义中心的访问控制 | 根据功能了解和定义每个组件在 IoT 中心解决方案中将[具有的访问类型](iot-security-deployment.md#securing-the-cloud)。 允许的权限是*注册表读取*、*注册表读取、**服务连接*和*设备连接*。 [IoT 中心中的默认共享访问策略](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)还有助于根据每个组件的角色定义权限。 | - |
| 定义后端服务的访问控制 | IoT 中心解决方案引入的数据可由其他 Azure 服务（如[Cosmos DB、](https://docs.microsoft.com/azure/cosmos-db/)[流分析](https://docs.microsoft.com/azure/stream-analytics/)、[应用服务](https://docs.microsoft.com/azure/app-service/)、[逻辑应用](https://docs.microsoft.com/azure/logic-apps/)和 Blob[存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)）使用。 确保了解和允许这些服务记录的适当访问权限。 | - |

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 安全设备身份验证 | 通过使用[唯一的身份密钥或安全令牌](iot-security-deployment.md#iot-hub-security-tokens)，或每个[设备的设备上 X.509 证书](iot-security-deployment.md#x509-certificate-based-device-authentication)，确保设备和 IoT 中心之间的安全通信。 使用适当的方法[根据所选协议（MQTT、AMQP 或 HTTPS）使用安全令牌](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 | - |
| 安全设备通信 | IoT 集线器使用传输层安全 （TLS） 标准（支持版本 1.2 和 1.0）确保与设备的连接。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)确保最大的安全性。 | - |
| 保护服务通信 | IoT 中心提供终结点以仅使用 TLS 协议连接到后端服务（如[Azure 存储](/azure/storage/)或[事件中心](/azure/event-hubs)），并且未加密通道上不会公开终结点。 一旦此数据到达这些后端服务进行存储或分析，请确保为该服务使用适当的安全和加密方法，并保护后端的敏感信息。 | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 保护对设备的访问 | 将设备中的硬件端口保持在最低限度，以避免不必要的访问。 此外，建立机制以防止或检测设备的物理篡改。 有关详细信息，请阅读[IoT 安全最佳实践](iot-security-best-practices.md)。 | - |
| 构建安全硬件 | 集成加密存储或可信平台模块 （TPM） 等安全功能，确保设备和基础架构更加安全。 保持设备操作系统和驱动程序升级到最新版本，如果空间允许，安装防病毒和反恶意软件功能。 阅读[IoT 安全体系结构](iot-security-architecture.md)，了解这如何帮助缓解多种安全威胁。 | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 监控对设备的未授权访问 |  使用设备操作系统的日志记录功能监视设备或其端口的任何安全漏洞或物理篡改。 | - |
| 从云监控 IoT 解决方案 | 使用[Azure 监视器 中的指标](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)监视 IoT 中心解决方案的总体运行状况。 | - |
| 设置诊断 | 通过在解决方案中记录事件，然后将诊断日志发送到 Azure 监视器，以深入了解性能，密切监视操作。 有关详细信息，请阅读[IoT 中心中的监视器并诊断问题](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)。 | - |

## <a name="next-steps"></a>后续步骤

对于涉及 Azure IoT 的高级方案，您可能需要考虑其他安全要求。 有关详细信息[，请参阅 IoT 安全体系结构](iot-security-architecture.md)。

