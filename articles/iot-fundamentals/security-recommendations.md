---
title: 针对 Azure IoT 的安全建议 |Microsoft Docs
description: 本文汇总了其他步骤以确保你的 Azure IoT 中心解决方案中的安全性。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.openlocfilehash: d079e082fb8ac90f398e46f283bd1e33e2b4ab40
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463079"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>针对 Azure 物联网 (IoT) 部署的安全建议

本文包含 Azure IoT 服务的安全建议。 实现这些建议将帮助你满足作为一名客户的 Azure IoT 中，并将你的安全义务改善 IoT 解决方案的总体安全性。 通过 Azure IoT 提供的内部函数的安全功能的详细信息，请阅读[从源头保障 IoT 的安全](iot-security-ground-up.md)。

## <a name="general"></a>常规

| 建议 | 注释 |
|-|-|
| 保持最新状态 | 使用最新版本的受支持的平台，编程语言、 协议和框架。 |
| 保持身份验证密钥处于安全 | 确保设备 Id 和其身份验证密钥以物理方式安全部署后。 这可以避免恶意设备伪装为已注册的设备。 |
| 使用设备 Sdk 在可能的情况 | 设备 Sdk 实现各种安全功能，如加密、 身份验证，等，以帮助您开发功能强大且安全的设备应用程序。 请参阅[了解和使用 Azure IoT 中心 Sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)有关详细信息。 |


## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 |
|-|-|
| 定义为中心的访问控制 | [了解并定义的访问类型](iot-security-deployment.md#securing-the-cloud)每个组件会在 IoT 中心解决方案中，基于的功能。 允许的权限都*注册表读取*， *RegistryReadWrite*， *ServiceConnect*，以及*DeviceConnect*。 默认值[共享访问策略中的 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)还可以帮助定义每个组件根据其角色的权限。 |
| 定义后端服务的访问控制 | 引入你的 IoT 中心解决方案的数据可供其他 Azure 服务等[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)， [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)，[应用服务](https://docs.microsoft.com/azure/app-service/)，[逻辑应用](https://docs.microsoft.com/azure/logic-apps/)，并[Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。 请务必了解，并允许适当的访问权限，这些服务的所述。 |


## <a name="data-protection"></a>数据保护

| 建议 | 注释 |
|-|-|
| 安全设备身份验证 | 通过使用确保设备与 IoT 中心之间的通信安全[唯一标识密钥或安全令牌](iot-security-deployment.md#iot-hub-security-tokens)，或[的设备上的 X.509 证书](iot-security-deployment.md#x509-certificate-based-device-authentication)为每个设备。 使用到适当的方法[使用基于所选协议 （MQTT、 AMQP 或 HTTPS） 的安全令牌](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 |
| 保护设备的通信 | IoT 中心可保护与使用传输层安全性 (TLS) 标准的设备的连接支持版本 1.2、 1.2 和 1.0。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)以确保最高的安全性。 |
| 保护服务通信 | IoT 中心提供终结点，如连接到后端服务[Azure 存储](/azure/storage/)或[事件中心](/azure/event-hubs)使用仅 TLS 协议和任何终结点会在未加密的通道上公开。 此数据到达后用于存储或分析这些后端服务，请确保使用该服务的适当的安全和加密方法和保护敏感信息由后端。 |


## <a name="networking"></a>网络

| 建议 | 注释 |
|-|-|
| 保护对你的设备的访问 | 硬件端口中保留你的设备到最低，以避免不必要的访问。 此外，生成机制，以防止或检测物理篡改的设备。 读取[IoT 安全最佳实践](iot-security-best-practices.md)有关详细信息。 |
| 构建安全的硬件 | 合并如加密的存储或受信任的平台模块 (TPM)，以使设备和基础结构更加安全的安全功能。 保留的设备的操作系统和驱动程序已升级到最新版本，如果空间允许，安装防病毒和反恶意软件功能。 读取[IoT 安全体系结构](iot-security-architecture.md)若要了解这如何帮助减少几个安全威胁。 |


## <a name="monitoring"></a>监视

| 建议 | 注释 |
|-|-|
| 监视你的设备的未经授权的访问 |  使用设备操作系统的日志记录功能来监视任何安全漏洞或设备或其端口的物理篡改。 |
| 监视云环境带来的 IoT 解决方案 | 监视 IoT 中心的解决方案使用的总体运行状况[Azure Monitor 中的指标](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)。 |
| 设置诊断 | 在解决方案中，记录事件并然后将诊断日志发送到 Azure Monitor 来查看性能密切关注您的操作。 读取[监视和诊断中 IoT 中心的问题](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)有关详细信息。 |

## <a name="next-steps"></a>后续步骤

对于涉及 Azure IoT 的高级方案，可能需要考虑其他安全要求。 请参阅[IoT 安全体系结构](iot-security-architecture.md)有关更多指导。

