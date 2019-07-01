---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173076"
---
1. 使用 `dps-keygen` 命令行实用程序生成连接字符串：

    若要安装[密钥生成器实用程序](https://github.com/Azure/dps-keygen)，请运行以下命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 若要生成连接字符串，请使用之前记下的连接详细信息运行以下命令：

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 复制 `dps-keygen` 输出中的连接字符串，以便在设备代码中使用。