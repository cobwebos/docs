---
title: 对语音设备 SDK-语音服务进行故障排除
titleSuffix: Azure Cognitive Services
description: 本文提供信息以帮助你解决问题时使用语音设备 SDK 可能会遇到。
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606301"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>排查语音设备 SDK 问题

本文提供信息以帮助你解决问题时使用语音设备 SDK 可能会遇到。

## <a name="certificate-failures"></a>证书错误

如果使用语音服务时，则会出现证书故障，，请确保你的设备具有正确的日期和时间：

1. 转到“设置”  。 在“系统”下选择“日期和时间”。  

    ![在“设置”下选择“日期和时间”](media/speech-devices-sdk/qsg-12.png)

1. 将“自动日期和时间”选项保持选中状态。  在“选择时区”下，选择你的当前时区。 

    ![选择日期和时区选项](media/speech-devices-sdk/qsg-13.png)

    看到开发工具包的时间与电脑上的时间匹配时，表示开发工具包已连接到 Internet。

## <a name="next-steps"></a>后续步骤

* [查看发行说明](devices-sdk-release-notes.md)
