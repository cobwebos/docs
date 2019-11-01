---
title: 关于 Azure Kinect DK
description: Azure Kinect DK 概述
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, 概述, 开发人员工具包, DK, 设备, 深度, 人体跟踪, 语音, 认知服务, SDK, 固件
ms.openlocfilehash: e3b8464a1828f0c1588d353f5c4d74966c90f549
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800240"
---
# <a name="about-azure-kinect-dk"></a>关于 Azure Kinect DK

Azure Kinect DK 是一款开发人员工具包，配有先进的 AI 传感器，提供复杂的计算机视觉和语音模型。  Kinect 将深度传感器、空间麦克风阵列与视频摄像头和方向传感器整合成一体式的小型设备，提供多种模式、选项和软件开发工具包 (SDK)。 它可在 [Microsoft 在线商店](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq)中购买。

Azure Kinect DK 开发环境由以下多个 SDK 组成：

- 用于访问低级别传感器和设备的传感器 SDK。
- 用于跟踪 3D 人体的人体跟踪 SDK。
- 用于启用麦克风访问和基于 Azure 云的语音服务的语音认知服务 SDK。

此外，可将认知视觉服务与设备 RGB 相机配合使用。

   ![Azure Kinect SDK 示意图](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect 传感器 SDK

Azure Kinect 传感器 SDK 提供低级别传感器访问用于完成 Azure Kinect DK 硬件传感器和设备配置。

若要详细了解 Azure Kinect 传感器 SDK，请参阅[使用传感器 SDK](about-sensor-sdk.md)。

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect 传感器 SDK 功能

传感器 SDK 提供以下功能，这些功能一经安装即可在 Azure Kinect DK 上运行：

- 深度相机访问和模式控制（被动 IR 模式，以及宽视场和窄视场深度模式） 
- RGB 相机访问和控制（例如曝光和白平衡） 
- 运动传感器（陀螺仪和加速度传感器）访问 
- 同步的深度 RGB 相机流，相机之间的延迟可配置 
- 外部设备同步控制，设备之间的延迟偏移量可配置 
- 用于处理图像分辨率、时间戳等的相机帧元数据访问。 
- 设备校准数据访问 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect 传感器 SDK 工具

传感器 SDK 中提供了以下工具：

- 查看器工具，可用于监视设备数据流和配置不同的模式。
- 使用 Matroska 容器格式的传感器录制工具和播放读取器 API。
- Azure Kinect DK 固件更新工具。

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect 人体跟踪 SDK

人体跟踪 SDK 包含 Windows 库和运行时，在 Azure Kinect DK 硬件上使用时可以跟踪 3D 人体。

### <a name="azure-kinect-body-tracking-features"></a>Azure Kinect 人体跟踪功能

随附的 SDK 提供以下人体跟踪功能：

- 提供人体图像分段。
- 包含 FOV 中每个不完整或完整人体的在解剖学上正确的骨干。
- 提供每个人体的唯一标识。
- 可跟踪人体在不同时间的位置。

### <a name="azure-kinect-body-tracking-tools"></a>Azure Kinect 人体跟踪工具

- 人体跟踪器提供一个查看器工具用于跟踪 3D 人体。

## <a name="speech-cognitive-services-sdk"></a>语音认知服务 SDK

语音 SDK 启用已连接到 Azure 的语音服务。

### <a name="speech-services"></a>语音服务

- 语音转文本
- 语音翻译
- 文本转语音

>[!NOTE]
>Azure Kinect DK 没有扬声器。

有关更多详细信息，请访问[语音服务文档](https://docs.microsoft.com/azure/cognitive-services/speech-service/)。

## <a name="vision-services"></a>视觉服务

以下 [Azure 认知视觉服务](https://azure.microsoft.com/services/cognitive-services/directory/vision/)提供可在图像与视频中识别和分析内容的 Azure 服务。

- [计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [人脸](https://azure.microsoft.com/services/cognitive-services/face/)
- [视频索引器](https://azure.microsoft.com/services/media-services/video-indexer/)
- [内容审查器](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

服务在不断发展和完善，因此，请记得定期检查新的或补充的[认知服务](https://azure.microsoft.com/services/cognitive-services/)以改进应用程序。 若要抢先了解新兴服务，请查看[认知服务实验室](https://labs.cognitive.microsoft.com/)。

## <a name="azure-kinect-hardware-requirements"></a>Azure Kinect 硬件要求

Azure Kinect DK 将 Microsoft 的最新传感器技术集成到了与 USB 连接的单个附件中。 有关详细信息，请参阅 [Azure Kinect DK 硬件规格](hardware-specification.md)。

## <a name="next-steps"></a>后续步骤

现在你已了解 Azure Kinect DK 的概况。 接下来，请深入到它的每项功能并进行设置！

> [!div class="nextstepaction"]
>[快速入门：设置 Azure Kinect DK](set-up-azure-kinect-dk.md)
