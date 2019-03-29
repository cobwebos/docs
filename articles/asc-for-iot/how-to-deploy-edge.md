---
title: 部署 IoT Edge 模块 ASC |Microsoft Docs
description: 了解如何将 ASC 部署为 IoT Edge 上的 IoT 安全代理。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580488"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>部署 IoT Edge 设备上的安全模块

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

适用于 IoT 的 ASC **azureiotsecurity**模块的 IoT Edge 设备提供全面的安全解决方案。
安全模块收集、 聚合和分析来自你的操作系统和容器系统的原始安全数据到切实可行的安全建议和警报。
若要了解详细信息，请参阅[IoT Edge 的安全模块](security-edge-architecture.md)。

在本指南中，您将了解如何部署 IoT Edge 设备上的安全模块。

## <a name="deploy-security-module"></a>部署安全模块

使用以下步骤来为 IoT Edge 部署 IoT 安全模块 ASC。

### <a name="prerequisites"></a>必备组件

1. 请确保你的设备[注册为 IoT Edge 设备](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal)。

1. ASC 为 IoT Edge 模块需要[审核框架](https://linux.die.net/man/8/auditd)边缘设备上安装。

   在边缘设备上运行以下命令来安装 framework:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>使用 Azure 门户进行部署

1. 打开**Marketplace**在 Azure 门户中。

1. 搜索**azure iot 安全**，然后单击**Azure IoT 安全**。

   ![](media/howto/edge_onboarding_7.png)

1. 单击“创建”。

1. 选择你**订阅**， **IoT 中心**并**IoT Edge 设备名称**，然后单击**创建**。

1. 单击**下一步**两次到**评审部署**。

1. 请确保**edgeHub.settings.createOptions**配置，如下所示：

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. 单击**提交**以完成部署。


## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请转到模块配置的操作方法指南。 
> [!div class="nextstepaction"]
> [模块配置操作指南](./how-to-agent-configuration.md)