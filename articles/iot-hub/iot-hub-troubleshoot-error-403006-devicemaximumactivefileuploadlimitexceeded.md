---
title: 排查 Azure IoT 中心错误 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: 了解如何修复错误 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960836"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

本文介绍**403006 DeviceMaximumActiveFileUploadLimitExceeded**错误的原因和解决方案。

## <a name="symptoms"></a>症状

文件上传请求失败，错误代码为**403006** ，消息为 "活动文件上传请求数不能超过 10"。

## <a name="cause"></a>原因

每个设备客户端限制为[10 个并发文件上传](./iot-hub-devguide-quotas-throttling.md#other-limits)。 

如果设备在文件上传完成时没有通知 IoT 中心，你可以轻松地超过限制。 此问题通常由不可靠的设备端网络引起。

## <a name="solution"></a>解决方案

确保设备可以及时[通知 IoT 中心文件上传已完成](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)。 然后，尝试[降低用于文件上传配置的 SAS 令牌 TTL](iot-hub-configure-file-upload.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关文件上传的详细信息，请参阅使用[IoT 中心上传文件](./iot-hub-devguide-file-upload.md)和[使用 Azure 门户配置 Iot 中心文件上传](./iot-hub-configure-file-upload.md)。