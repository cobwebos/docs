---
title: 远程监视解决方案导入 Edge 包 - Azure | Microsoft Docs
description: 本文介绍如何将 IoT Edge 包导入到远程监视解决方案加速器
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442843"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>将 IoT Edge 包导入到远程监视解决方案加速器

部署清单定义要部署到 IoT Edge 设备的模块。 本文假设组织中的开发人员已创建部署清单。 若要了解开发人员如何创建清单，请参阅以下 IoT Edge 操作说明文章之一：

- [通过 Azure 门户部署 Azure IoT Edge 模块](../iot-edge/how-to-deploy-modules-portal.md)
- [使用 Azure CLI 部署 Azure IoT Edge 模块](../iot-edge/how-to-deploy-modules-cli.md)
- [通过 Visual Studio Code 部署 Azure IoT Edge 模块](../iot-edge/how-to-deploy-modules-vscode.md)

开发人员在开发环境中创建并测试部署清单。 准备就绪后，可以将清单导入到远程监视解决方案加速器。

## <a name="export-a-manifest"></a>导出清单

使用 Azure 门户从开发环境中导出部署清单：

1. 在 Azure 门户中，导航到用于开发和测试 IoT Edge 设备的 IoT 中心。 单击“IoT Edge”，然后单击“IoT Edge 部署”   ：![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. 单击包含要使用的部署配置的部署。 此时将显示“部署详细信息”  页：![IoT Edge 部署详细信息](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. 单击“下载 IoT Edge 清单”  ：![下载部署清单](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. 将 JSON 文件另存为名为 **deploymentmanifest.json** 的本地文件。

现在你有一个包含部署清单的文件。 在下一部分，将此清单作为包导入到远程监视解决方案中。

## <a name="import-a-package"></a>导入包

按照以下步骤将 Edge 部署清单作为包导入到解决方案中：

1. 导航到远程监视 Web UI 中的“包”页面  ：![包页面](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. 单击“+ 新建包”，选择“Edge 清单”作为包类型，然后单击“浏览”以选择在上一部分中保存的 **deploymentmanifest.json** 文件    ：![选择清单](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. 单击“上载”将包添加到远程监视解决方案中  ：![上传的包](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

现在已将 IoT Edge 部署清单作为包上传。 在“部署”页面上，可以将此包部署到已连接的 IoT Edge 设备  。

## <a name="next-steps"></a>后续步骤

现在你已经了解如何将模块部署到远程监视解决方案中的 IoT Edge 设备，下一步将了解有关 [IoT Edge](../iot-edge/about-iot-edge.md) 的详细信息。
