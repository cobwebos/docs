---
title: 使用 Jenkins 插件启用 CI/CD - Azure IoT Edge | Microsoft Docs
description: 借助适用于 Jenkins 的 Azure IoT Edge 扩展，可将 IoT Edge 开发和部署任务集成到现有的 DevOps 解决方案。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910818"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>将 Azure IoT Edge 与 Jenkins 管道集成

Azure IoT Edge 具有对 Azure DevOps 和 Azure DevOps Projects 的内置支持，并提供可将 DevOps 功能扩展到 Jenkins 的插件。 [Jenkins](https://jenkins.io/) 是开源自动化服务器，它使用插件来支持许多类型的开发和部署项目，包括 IoT Edge。 

适用于 Jenkins 的 Azure IoT Edge 插件重点关注持续集成和持续部署。 可创建一个生成和推送管道，用于生成模块并将其容器映像推送到容器注册表。 然后，创建将模块部署到 IoT Edge 设备的发布管道。 

在开始使用适用于 Jenkins 的 IoT Edge 插件前，需要一个 Azure IoT 中心和一个容器注册表来保存容器映像。 使用 Azure 服务主体向 IoT 中心授予 Jenkins 参与者权限，以便该插件可以为 IoT Edge 设备创建部署。 

如果已准备好开始，请查找[适用于 Jenkins 的 Azure IoT Edge 插件](https://plugins.jenkins.io/azure-iot-edge)的安装和使用详细信息。