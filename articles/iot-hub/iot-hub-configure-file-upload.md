---
title: "使用 Azure 门户配置文件上载 | Microsoft Docs"
description: "如何使用 Azure 门户配置 IoT 中心，以便从连接的设备上传文件。 包括有关配置目标 Azure 存储帐户的信息。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: b1e32eef393aa213cac6af68821ad0572e4a3c92
ms.lasthandoff: 04/07/2017


---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>使用 Azure 门户配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>文件上载

若要使用 [IoT 中心的文件上传功能][lnk-upload]，必须先将 Azure 存储帐户与中心关联。 选择“**文件上载**”设置，即可显示正在修改的 IoT 中心的文件上载属性列表。

![在门户中查看 IoT 中心文件上传设置][13]

**存储容器**：使用 Azure 门户在当前 Azure 订阅中选择 Azure 存储帐户中的 blob 容器，以便与 IoT 中心关联。 如有必要，可以在“存储帐户”边栏选项卡上创建 Azure 存储帐户，并在“容器”边栏选项卡上创建 blob 容器。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

![在门户中查看用于文件上传的存储容器][14]

**接收已上载文件的通知**：通过切换来启用或禁用文件上载通知。

**SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时，但可以使用滑块自定义为其他值。

**文件通知设置默认 TTL**：文件上载通知到期前的生存时间。 默认设置为一天，但可以使用滑块自定义为其他值。

**文件通知最大传送数**：IoT 中心将尝试传送文件上载通知的次数。 默认设置为 10，但可以使用滑块自定义为其他值。

![在门户中配置 IoT 中心文件上传][15]

## <a name="next-steps"></a>后续步骤

有关 IoT 中心文件上传功能的详细信息，请参阅 IoT 中心开发人员指南中的[从设备上传文件][lnk-upload]。

若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 设备][lnk-bulk]
* [IoT 中心度量值][lnk-metrics]
* [操作监视][lnk-monitor]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]
* [从根本上保护 IoT 解决方案][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

