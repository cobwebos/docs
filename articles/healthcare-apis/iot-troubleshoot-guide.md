---
title: 适用于 FHIR 的 Azure IoT 连接器（IoT 连接器）-疑难解答指南和操作方法
description: 如何排查常见的 IoT Connector 错误消息和条件并复制映射文件
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285571"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>IoT 连接器（预览版）疑难解答指南

本文介绍常见 IoT Connector （预览版）错误消息和条件的疑难解答步骤。  

你还将了解如何在 Azure 门户之外创建用于编辑和存档的 IoT Connector （预览版） JSON 映射文件的副本。

## <a name="error-messages-and-fixes"></a>错误消息和修补程序

|消息   |条件  |Fix         |
|----------|-----------|------------|
|映射名称无效，映射名称应为 device 或 FHIR|提供的映射类型不是设备或 FHIR|使用两种受支持的映射类型之一（例如： Device 或 FHIR）|
|未定义重新生成密钥参数|重新生成密钥请求|在重新生成密钥请求中包含参数|
|已达到此订阅中可设置的 IoT 连接器实例的最大数量|已达到 IoT 连接器订阅配额（默认值为每个订阅2个）|删除一个现有连接器，使用每个订阅配额未达到（2）个连接器或请求订阅配额增加的其他订阅|
|启用 IoT 连接器的移动资源不支持用于 FHIR 资源的 Azure API|尝试在具有一个或多个 IoT 连接器的 FHIR 资源的 Azure API 上执行移动操作|删除现有连接器以执行/完成移动操作|
|用于 FHIR 资源的 Azure API 启用了专用链接。  IoT 连接器不支持专用链接|尝试将 IoT 连接器添加到启用了专用链接的 FHIR 资源的 Azure API|选择或创建未启用专用链接的 Azure API for FHIR 资源（版本 R4）|
|未预配 IoT 连接器|尚未设置父（IoT 连接器）时，尝试使用子服务（连接 & 映射）|预配 IoT 连接器|
|请求不受支持|不支持特定的 API 请求|使用正确的 API 请求|
|帐户不存在|尝试添加 IoT 连接器和 Azure API for FHIR 资源不存在|创建用于 FHIR 资源的 Azure API，然后重试该操作|
|IoT 连接器不支持 Azure API for FHIR 资源 FHIR 版本|尝试将 IoT 连接器用于 FHIR 资源的不兼容版本的 Azure API|创建新的 Azure API for FHIR 资源（版本 R4）或使用现有的 Azure API for FHIR 资源（版本 R4）

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>创建 IoT 连接器（预览版）映射文件的副本
> [!NOTE]
> JSON 是目前唯一受支持的设备和 FHIR 映射文件格式。

> [!TIP]
> 复制 IoT 连接器映射文件可用于在 Azure 门户网站外进行编辑和存档，并在打开支持票证时提供 Azure 技术支持。
> 
> 详细了解 IoT 连接器[设备和 FHIR 映射 JSON 文件](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. 在 **"外接程序"** 部分的 "用于 FHIR 的 Azure API" 资源仪表板左下方选择 **"IoT 连接器（预览版）** "。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 选择要从中复制映射文件的 **"连接器"** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. 选择 **"配置设备映射"**。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> 此过程也可用于复制/保存 **"配置 FHIR 映射"** JSON 的内容。

4. 选择 JSON 的内容并执行复制操作（例如：选择 Ctrl + c）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 连接器" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 执行 "粘贴" 操作（例如：选择 Ctrl + v）到编辑器中的新文件（例如： Visual Studio Code、记事本），并使用 *. json 扩展名保存文件。

> [!TIP]
> 如果要为 IoT 连接器打开[Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)票证，请确保包含映射文件的副本，以帮助进行故障排除过程。

## <a name="next-steps"></a>后续步骤

查看 IoT 连接器上的常见问题

>[!div class="nextstepaction"]
>[IoT 连接器常见问题](fhir-faq.md#iot-connector-preview)


FHIR 是 HL7 的注册商标，经 HL7 许可使用。