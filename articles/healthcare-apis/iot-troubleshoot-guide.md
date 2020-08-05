---
title: 适用于 FHIR 的 Azure IoT 连接器 (预览版) -疑难解答指南和操作方法
description: 如何排查常见的 Azure IoT Connector for FHIR (预览) 错误消息和条件以及复制映射文件
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553676"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>适用于 FHIR 的 Azure IoT 连接器 (预览版) 故障排除指南

本文提供了用于排查 FHIR * 错误消息和条件的常见 Azure IoT 连接器的步骤。  

你还将了解如何创建 Azure IoT Connector 的副本，以便在 Azure 门户之外进行编辑和存档，或在打开支持票证时提供 Azure 技术支持。 

## <a name="error-messages-and-fixes"></a>错误消息和修补程序

|Message   |条件  |Fix         |
|----------|-----------|------------|
|映射名称无效，映射名称应为 device 或 FHIR|提供的映射类型不是设备或 FHIR|使用两种受支持的映射类型之一 (例如： Device 或 FHIR) |
|未定义重新生成密钥参数|重新生成密钥请求|在重新生成密钥请求中包含参数|
|已达到可在此订阅中预配的 IoT 连接器 * 实例的最大数量|已达到 FHIR 订阅配额的 Azure IoT 连接器 (默认值为每个订阅 (2) ) |删除 FHIR 的 Azure IoT 连接器的现有实例之一，使用的订阅未达到订阅配额或请求订阅配额增加|
|启用 IoT 连接器的移动资源不支持用于 FHIR 资源的 Azure API|正在尝试对 Azure API for FHIR 资源执行移动操作，该资源具有一个或多个用于 FHIR 的 Azure IoT 连接器实例|删除 () Azure IoT 连接器的现有实例，FHIR 执行并完成移动操作|
|未预配 IoT 连接器|尝试使用子服务 (连接 & 映射) 父 (未预配 FHIR 的 Azure IoT 连接器) |为 FHIR 预配 Azure IoT 连接器|
|请求不受支持|不支持特定的 API 请求|使用正确的 API 请求|
|帐户不存在|尝试添加适用于 FHIR 的 Azure IoT 连接器和用于 FHIR 的 Azure API 资源不存在|创建用于 FHIR 资源的 Azure API，然后重试该操作|
|IoT 连接器不支持 Azure API for FHIR 资源 FHIR 版本|尝试将适用于 FHIR 的 Azure IoT 连接器与 Azure API for FHIR 资源的版本不兼容|创建新的 Azure API for FHIR 资源 (版本 R4) 或使用现有的 Azure API for FHIR 资源 (version R4) 

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>为 FHIR (预览创建 Azure IoT 连接器的副本) 映射文件
为 FHIR 映射文件复制 Azure IoT 连接器可用于在 Azure 门户网站外进行编辑和存档，并在打开支持票证时提供 Azure 技术支持。

> [!NOTE]
> JSON 是目前唯一受支持的设备和 FHIR 映射文件格式。

> [!TIP]
> 详细了解适用于 FHIR 设备的 Azure IoT 连接器[和 FHIR 映射 JSON 文件](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. 在 **"外接程序"** 部分的 "FHIR 资源" 仪表板的左下角，选择 **"IoT 连接器 (预览") "** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 选择要从中复制映射文件的 **"连接器"** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此过程也可用于复制和保存 **"配置 FHIR 映射"** JSON 的内容。

3. 选择 **"配置设备映射"**。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 选择 JSON 的内容并执行复制操作 (例如：选择 Ctrl + c) 。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 执行粘贴操作 (例如：在编辑器中选择 "Ctrl + v) 到新文件中 (例如： Visual Studio Code、记事本) 并使用 *. json 扩展名保存文件。

> [!TIP]
> 如果要为 FHIR 的 Azure IoT 连接器打开[Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)票证，请确保包含映射文件的副本，以帮助进行故障排除过程。

## <a name="next-steps"></a>后续步骤

查看有关适用于 FHIR 的 Azure IoT 连接器的常见问题。

>[!div class="nextstepaction"]
>[适用于 FHIR 常见问题的 Azure IoT 连接器](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* 在 Azure 门户中，用于 FHIR 的 Azure IoT 连接器称为 IoT 连接器 (预览版) 。

FHIR 是 HL7 的注册商标，经 HL7 许可使用。