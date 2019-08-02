---
title: include 文件
description: include 文件
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640452"
---
* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* **Windows**

    * [Python 2.x 或](https://www.python.org/downloads/)3.x。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip- Python 包管理系统](https://pip.pypa.io/en/stable/installing/)。

    * 如果你使用的是 Windows OS, 请确保具有正确版本的[视觉C++可再发行组件包](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads), 以允许使用 Python 中的本机 dll。 建议使用最新版本。

    * 如果需要, 请使用命令安装[iothub](https://pypi.org/project/azure-iothub-device-client/)包`pip install azure-iothub-device-client`

    * 如果需要, 请使用命令安装[iothub](https://pypi.org/project/azure-iothub-service-client/)包`pip install azure-iothub-service-client`

* **Mac OS**

    对于 Mac OS, 需要 Python 3.7.0 (或 2.7) + libboost-1.67 + 卷曲 7.61.1 (所有通过 homebrew 安装)。 任何其他分发/OS 可能会嵌入不同版本的提升 & 依赖项, 这些依赖项不起作用, 将导致在运行时 ImportError。

    适用于 `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 包目前仅供 Windows OS 使用。 对于 Linux/Mac OS, 请参阅为[Python 发布准备开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)中的 linux 和 Mac OS 特定部分。

> [!NOTE]
> 导入示例中的 iothub_client 时出现了几个错误报告。 有关处理**ImportError**问题的详细信息, 请参阅[处理 ImportError 问题](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)。
