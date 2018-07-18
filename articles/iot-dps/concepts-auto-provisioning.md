---
title: IoT 中心设备预配服务 - 自动预配的概念
description: 本文提供使用 IoT 设备预配服务、IoT 中心和客户端 SDK 自动预配设备所要经历的阶段的概念性概述。
author: BryanLa
ms.author: bryanla
ms.date: 06/01/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: a5ac8b6116eebb400c12d50de010b93bded268ff
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736400"
---
# <a name="auto-provisioning-concepts"></a>自动预配的概念

如[概述](about-iot-dps.md)中所述，设备预配服务是一个帮助程序服务，可将设备实时预配到 IoT 中心，而无需人工干预。 成功预配后，设备会直接连接到指定的 IoT 中心。 此过程称为自动预配，可为设备提供现成的注册和初始配置体验。

## <a name="overview"></a>概述

可将 Azure IoT 自动预配划分为以下三个阶段：

1. **服务配置** - Azure IoT 中心和 IoT 中心设备预配服务实例的一次性配置，建立这些实例并在它们之间创建链接。

   > [!NOTE]
   > 无论 IoT 解决方案的大小如何，也不管是否要支持数百万台设备，此阶段也是**一次性配置**。

2. **设备登记** - 使设备预配服务实例将来识别尝试注册的设备的过程。 [登记](concepts-service.md#enrollment)是通过在预配服务中配置设备标识信息来实现的。可以针对单个设备进行“单独登记”，也可以针对多个设备进行“组登记”。 标识基于设备应该使用的[认证机制](concepts-security.md#attestation-mechanism)，使预配服务在注册过程中认证设备的真实性：

   - **TPM**：配置为“单独登记”的设备标识基于 TPM 注册 ID 和公共认可密钥。 既然 TPM 是一种[规范](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)，服务期望只会根据规范进行认证，而不考虑 TPM 实现方式（硬件或软件）如何。 有关基于 TPM 的认证的详细信息，请参阅[设备预配：使用 TPM 进行标识认证](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/)。 

   - **X509**：配置为“单独登记”或“组登记”的设备标识基于作为 .pem 或 .cer 文件上传到登记组件的 X.509 数字证书。

   > [!IMPORTANT]  
   > 尽管硬件安全模块 (HSM) 并不是使用设备预配服务的先决条件，但我们强烈建议在设备中使用 HSM 来存储敏感设备标识信息，例如密钥和 X.509 证书。

3. **设备注册和配置** - 由注册软件在引导时启动，使用适用于设备和认证机制的设备预配服务客户端 SDK 生成。 软件与预配服务建立连接，以便对设备进行身份验证，以及在 IoT 中心进行后续注册。 注册成功后，将为设备提供 IoT 中心唯一设备 ID 和连接信息，使其能够提取初始配置并开始执行遥测过程。 在生产环境中，此阶段可能会在完成前两个阶段数周或数月后发生。

## <a name="roles-and-operations"></a>角色和操作

由于制造时间、交货、报关等生产因素，前一部分中所述的阶段可能会跨数周或数月。此外，如果涉及到多种实体，这些阶段可能需要经历多个角色的活动。 本部分更深入地探讨每个阶段相关的各种角色和操作，然后在顺序示意图中演示流程。 

自动预配还对设备制造商施加启用认证机制方面的要求。 制造操作也可以独立于自动预配阶段的时间发生，尤其是在建立自动预配后采购新设备的情况下。

左侧目录中提供了快速入门系列教程，旨在帮助通过实践解释自动预配。 为了促进/简化学习过程，我们将使用软件来模拟用于登记和注册的物理设备。 某些快速入门要求完成多个角色的操作，包括不存在的角色的操作（因为快速入门中所述的设备是模拟性的）。

| 角色 | Operation | 说明 | 
|------| --------- | ------------| 
| 制造商 | 为标识和注册 URL 编码 | 根据所用的认证机制，制造商需负责为设备标识信息和设备预配服务注册 URL 编码。<br><br>**快速入门**：由于设备是模拟的，因此不存在制造商角色。 有关如何获取这些信息以便在编写示例注册应用程序代码时使用的详细信息，请参阅“开发人员角色”。 |  
| | 提供设备标识 | 作为设备标识信息的发起者，制造商需负责通信向操作员（或指定的代理）传递这些信息，或者通过 API 将其直接登记到设备预配服务。<br><br>**快速入门**：由于设备是模拟的，因此不存在制造商角色。 有关如何获取在设备预配服务实例中登记模拟设备时所用的设备标识的详细信息，请参阅“操作员角色”。 | 
| 运算符 | 配置自动预配 | 此操作对应于自动预配的第一阶段。<br><br>**快速入门**：你需要履行操作员角色，在 Azure 订阅中配置设备预配服务和 IoT 中心实例。 | 设置自动预配 |
|  | 登记设备标识 | 此操作对应于自动预配的第二个阶段。<br><br>**快速入门**：你需要履行操作员角色，在设备预配服务实例中登记模拟设备。 设备标识由快速入门中模拟的认证方法（TPM 或 X.509）确定。 有关认证详细信息，请参阅“开发人员角色”。 | 
| 设备预配服务<br>IoT 中心 | \<所有操作\> | 对于使用物理设备的生产实现和使用模拟设备的快速入门，需要通过 Azure 订阅中配置的 IoT 服务履行这些角色。 角色/操作的功能完全相同，因为 IoT 服务预配物理设备和模拟设备的方式没有差别。 | 
| 开发人员 | 生成/部署注册软件 | 此操作对应于自动预配的第三个阶段。 开发人员负责使用相应的 SDK 生成注册软件并将其部署到设备。<br><br>**快速入门**：生成的示例注册应用程序模拟适用于所选平台/语言的、在工作站中运行的真实设备（而不是将其部署到物理设备）。 注册应用程序执行的操作与部署到物理设备的应用程序相同。 需要指定认证方法（TPM 或 X.509 证书），以及设备预配服务实例的注册 URL 和“ID 范围”。 设备标识由 SDK 认证逻辑在运行时根据指定的方法确定： <ul><li>**TPM 认证** - 开发工作站运行 [TPM 模拟器应用程序](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator)。 运行后，将使用单独的应用程序来提取 TPM 的“认可密钥”和“注册 ID”用于登记设备标识。 SDK 认证逻辑在注册期间也使用模拟器来提供签名的 SAS 令牌用于身份验证和登记验证。</li><li>**X509 认证** - 使用工具[生成证书](how-to-use-sdk-tools.md#x509-certificate-generator)。 生成后，需创建用于登记的证书文件。 SDK 认证逻辑在注册期间也使用该证书来提供身份验证和登记验证。</li></ul> | 
| 设备 | 启动和注册 | 此操作对应于自动预配的第三个阶段，由开发人员生成的设备注册软件执行。 有关详细信息，请参阅“开发人员角色”。 首次启动时： <ol><li>应用程序根据开发期间指定的全局 URL 和服务“ID 范围”，与设备预配服务实例建立连接。</li><li>连接后，将会根据登记期间指定的认证方法和标识对设备进行身份验证。</li><li>完成身份验证后，设备将注册到预配服务实例指定的 IoT 中心实例。</li><li>注册成功后，向注册应用程序返回唯一的设备 ID 和 IoT 中心终结点，以便与 IoT 中心通信。</li><li> 从此时起，设备可以提取用于配置的初始[设备孪生](~/articles/iot-hub/iot-hub-devguide-device-twins.md)状态，并开始执行报告遥测数据的过程。</li></ol>**快速入门**：由于设备是模拟的，因此注册软件在开发工作站上运行。| 

下图汇总了设备自动预配期间的角色和操作顺序：
<br><br>
[![设备的自动预配顺序](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> （可选）制造商还可以使用设备预配服务 API（而不是通过操作员）执行“登记设备标识”操作。 有关此顺序和其他要素的详细讨论，请观看[自动将设备注册到 Azure IoT](https://myignite.microsoft.com/sessions/55087) 视频（从 41:00 标记处开始）

## <a name="next-steps"></a>后续步骤

我们建议将本文加入书签，以便在学习相应自动预配快速入门的过程中参考。 

首先请完成最适合你偏好的管理工具的“设置自动预配”快速入门，其中逐步讲解了“服务配置”阶段：

- [使用 Azure CLI 设置自动预配](quick-setup-auto-provision-cli.md)
- [使用 Azure 门户设置自动预配](quick-setup-auto-provision.md)
- [使用资源管理器模板设置自动预配](quick-setup-auto-provision-rm.md)

然后继续学习最适合你偏好的设备认证机制和设备预配服务 SDK/语言的“自动预配模拟设备”快速入门。 此快速入门逐步讲解“设备登记”与“设备注册和配置”阶段： 

|  | 模拟设备认证机制 | 快速入门 SDK/语言 |  |
|--|--|--|--|
|  | 受信任的平台模块 (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509 证书 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




