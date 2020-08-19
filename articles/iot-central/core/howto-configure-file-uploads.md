---
title: 将文件从设备上传到 Azure 存储 |Microsoft Docs
description: 如何配置从设备到云的文件上传。 配置文件上传后，在设备上实现文件上传。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 6b717fd15b25ae4abd2af3520dba2e72f8f9f3a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556223"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>将文件从设备上传到云

*本主题适用于管理员和设备开发人员。*

IoT Central 允许你将媒体和其他文件从连接的设备上传到云存储。 在 IoT Central 应用程序中配置文件上传功能，然后在设备代码中实现文件上传。

## <a name="prerequisites"></a>先决条件

你必须是 IoT Central 应用程序中的管理员才能配置文件上传。

需要一个 Azure 存储帐户和容器来存储上传的文件。 如果没有要使用的现有存储帐户和容器，请 [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="configure-device-file-uploads"></a>配置设备文件上传

配置设备文件上传：

1. 导航到应用程序中的 " **管理** " 部分。

1. 选择 " **设备文件上传**"。

1. 选择要使用的存储帐户和容器。 如果该存储帐户与你的应用程序在不同的 Azure 订阅中，请输入存储帐户连接字符串。

1. 如有必要，请调整上传超时，以设置上载请求的有效时间。 有效值为1到24小时。

1. 选择“保存”。 如果状态显示为 " **已配置**"，则表示你已准备好从设备上传文件。

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="在应用程序中配置文件上传":::

## <a name="disable-device-file-uploads"></a>禁用设备文件上传

如果要禁用设备文件上传到 IoT Central 应用程序：

1. 导航到应用程序中的 " **管理** " 部分。

1. 选择 " **设备文件上传**"。

1. 选择“删除”。

## <a name="upload-a-file-from-a-device"></a>从设备上传文件

IoT Central 使用 IoT 中心的文件上传功能来启用设备上传文件。 有关演示如何从设备上传文件的示例代码，请参阅 [IoT Central 文件上传设备示例](https://docs.microsoft.com/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 IoT Central 中配置和实现设备文件上传，接下来建议的下一步是了解更多设备文件上传：

- [通过 IoT 中心将设备中的文件上传到云 (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [通过 IoT 中心将设备中的文件上传到云 (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [通过 IoT 中心将设备中的文件上传到云 (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [使用 IoT 中心将文件从设备上传到云 (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)
