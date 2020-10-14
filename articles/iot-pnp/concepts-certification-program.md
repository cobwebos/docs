---
title: Azure 认证的设备计划 |Microsoft Docs
description: 了解 Azure 认证的设备计划。
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048094"
---
# <a name="what-is-the-azure-certified-device-program"></a>什么是 Azure 认证的设备计划？

Azure 认证的设备计划可确保客户解决方案无缝地与 Azure 服务配合工作。 该计划使用工具、服务和应用商店，通过一种设备和解决方案组装人员来共享行业知识和最佳实践。

此程序旨在：

- **让客户自信：** 客户可以放心地购买 Microsoft 认证的设备，以便与 Azure 服务配合使用。

- **帮助客户找到适合其解决方案的设备：** 设备构建者可以在认证过程中发布其设备的独特功能。 客户可以轻松找到适合其需求的产品。

- **升级认证的设备：** 设备构建者获得了更高的可见性，与客户联系，并使用了 Azure 认证的设备品牌。

本文介绍如何：

- 将你的公司加入 Azure 认证的设备计划。
- 确定适用于你的设备的证书。
- 查找计划要求和其他资源以开始测试。
- 使用 Azure 认证的设备门户验证你的设备。

## <a name="onboarding"></a>登记

若要在 [Azure 认证的设备门户](https://aka.ms/acdp)上认证设备，必须完成以下步骤：

1. 确保公司使用工作或学校租户 Azure Active Directory 帐户。
2. 使用你的帐户加入 [Microsoft 合作伙伴网络 (MPN) ](https://partner.microsoft.com/) 。
3. 加入 MPN 后，登录到 [Azure 认证的设备门户](https://aka.ms/acdp) 。
4. 查看并签署 "公司配置文件" 页上的[程序协议](https://aka.ms/acdagreement)

### <a name="company-profile"></a>公司配置文件

若要在 Azure 认证的设备门户中管理公司的配置文件，请选择 " **公司配置文件**"。 公司配置文件包含公司 URL、电子邮件地址和徽标。 在继续执行任何证书操作之前，请接受此页上的计划协议。

Azure 认证设备目录中的设备描述页面使用公司配置文件信息。

## <a name="choose-the-certification"></a>选择证书

有三种不同的证书，每个证书重点提供不同的客户价值。 根据要生成的设备类型和目标受众，可以选择最适用的认证或认证：

### <a name="azure-certified-device"></a>Azure 认证设备

_Azure 认证设备认证_ 验证设备是否可以与 Azure IoT 中心连接，并通过设备预配服务 (DPS) 安全地进行配置。 此证书反映了设备的功能和互操作性，这是更高级证书的必要基线。

- 若要了解详细信息，请参阅 [证书要求](https://aka.ms/acdrequirements)。
- 若要了解有关使用 DPS 将设备连接到 Azure IoT 中心的详细信息，请参阅 [预配设备概述](../iot-dps/about-iot-dps.md)。

### <a name="iot-plug-and-play"></a>IoT 即插即用

_IoT 即插即用认证_是一种针对 Azure 认证设备认证的增量认证，可简化无需自定义设备代码构建设备的过程。 IoT 即插即用允许硬件合作伙伴构建可轻松地与基于 Azure IoT Central 和第三方解决方案的云解决方案集成的设备。

- 若要了解详细信息，请参阅 [证书要求](https://aka.ms/acdiotpnprequirements)。
- 若要了解有关如何为 IoT 即插即用测试准备设备的详细信息，请参阅 [如何认证 iot 即插即用设备](howto-certify-device.md)。

### <a name="edge-managed"></a>边缘托管

_边缘托管证书_，一种针对 Azure 认证设备认证的增量认证，重点介绍适用于运行 Windows、LINUX 或 Rto 的 azure IoT 设备的设备管理标准。 目前，此程序认证重点介绍了模块部署和管理的边缘运行时兼容性。

> [!TIP]
> 此程序以前称为 _IoT Edge 认证计划_。

- 若要了解详细信息，请参阅 [证书要求](https://aka.ms/acdedgemanagedrequirements)。
- 若要详细了解 IoT Edge，请参阅 [IoT Edge 概述](../iot-edge/about-iot-edge.md)。
- 若要了解有关受支持的操作系统和容器的详细信息，请参阅 [IoT Edge 支持的系统](../iot-edge/support.md)。

## <a name="use-the-azure-certified-device-portal"></a>使用 Azure 认证的设备门户

本部分概述如何使用 [Azure 认证的设备门户](https://certify.azure.com)。 若要了解详细信息，请参阅 [门户入门指南](https://aka.ms/acdhelp)。

若要在 Azure 认证的设备程序中验证设备，请完成以下四个步骤：

1. 提供设备详细信息
2. 测试设备
3. 提交并完成评审
4. 发布到 Azure 认证的设备目录 (可选) 

### <a name="provide-device-details"></a>提供设备详细信息

对于想要认证的每个设备，使用证书门户中的表单来记录有关设备硬件、设置说明和营销材料的详细信息：

- **设备信息：** 收集有关设备的信息，例如设备的名称、描述、硬件详细信息和操作系统。
- **入门指南**：客户可用于快速使用产品的 PDF 文档。 [示例模板](https://aka.ms/GSTemplate) 可用。
- **市场营销详情：** 为您的设备提供客户准备的营销信息，如图片和分发服务器信息。
- **其他行业认证：** 一个可选部分，可让你提供设备所具有的任何其他证书的相关信息。

### <a name="test-the-device"></a>测试设备

此阶段与设备交互，并在设备使用 DPS 连接到 IoT 中心后运行一系列测试。 完成后，可以查看一组包含设备测试结果的日志文件。

证书门户提供有关如何连接到用于测试的 IoT 中心实例的说明。 可以通过任何 [支持的证明方法](../iot-dps/concepts-service.md#attestation-mechanism)建立 DPS 连接。

Azure 认证的设备组可以与设备生成器联系，以便进一步手动验证设备。

### <a name="submit-and-publish"></a>提交并发布

最终要求的阶段是提交项目以供评审。 此步骤通知 Azure 认证的设备团队成员查看你的项目的完整性，包括设备和营销详细信息以及入门指南。 在批准之前，团队成员可以通过与问题相关的公司电子邮件地址与你联系。

如果你的设备需要进一步的手动验证作为认证的一部分，此时你会收到通知。

在认证设备后，你可以选择使用产品摘要页中的 " **发布到目录** " 功能将产品详细信息发布到 Azure 认证设备目录。

## <a name="if-you-have-questions"></a>如果有疑问

[iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)如果对认证计划、认证门户或 Azure 认证设备目录有任何疑问，请联系。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure 认证的设备计划，接下来是了解 [如何即插即用设备认证 IoT](howto-certify-device.md)。