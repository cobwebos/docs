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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803420"
---
1. 使用`dps-keygen`命令行实用工具，以生成连接字符串：

    若要安装[密钥生成器实用程序](https://github.com/Azure/dps-keygen)，运行以下命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 若要生成连接字符串，请运行以下命令使用前面所述的连接详细信息：

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 复制连接字符串从`dps-keygen`输出，以在设备代码中使用。