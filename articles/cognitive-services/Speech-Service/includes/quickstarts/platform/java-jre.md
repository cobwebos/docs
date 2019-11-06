---
title: 快速入门：用于 Java（Windows、Linux、macOS）的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，设置可将 Java（Windows、Linux、macOS）与语音服务 SDK 配合使用的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504337"
---
本指南介绍如何安装用于 64 位 Java 8 JRE 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Java 语音 SDK 包适用于以下操作系统：
  - Windows:仅 64 位
  - Mac：macOS X 10.13 或更高版本
  - Linux：在 Ubuntu 16.04、Ubuntu 18.04 或 Debian 9 上仅限 64 位

## <a name="prerequisites"></a>先决条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/)（要求已安装 Java）
- 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

  - 在 Ubuntu 上，请运行以下命令来安装所需的包：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - 在 Debian 9 上，请运行以下命令来安装所需的包：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 请注意，首次安装它时，可能需要重启 Windows 才能继续使用本指南。

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>创建 Eclipse 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
