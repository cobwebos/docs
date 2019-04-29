---
title: 远程监视解决方案导入程序包-Azure |Microsoft Docs
description: 本文介绍如何将自动设备管理包导入到远程监视解决方案加速器
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443265"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>将自动设备管理包导入到远程监视解决方案加速器

自动设备管理配置定义要部署到设备组的配置更改。 本文假设组织中的开发人员已创建自动设备管理配置。 若要了解开发人员如何创建配置，请参阅以下 IoT 中心操作说明文章之一：

- [使用 Azure 门户大规模配置和监视 IoT 设备](../iot-hub/iot-hub-auto-device-config.md)
- [使用 Azure CLI 大规模配置和监视 IoT 设备](../iot-hub/iot-hub-auto-device-config-cli.md)

开发人员在开发环境中创建和测试自动设备管理配置。 准备就绪后，可以将配置导入到远程监视解决方案加速器。

## <a name="export-a-configuration"></a>导出配置

使用 Azure 门户从开发环境导出自动设备管理配置：

1. 在 Azure 门户中，导航到用于开发和测试 IoT 设备的 IoT 中心。 单击“IoT 设备配置”：

    [![IoT 设备配置](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. 单击想要使用的配置。 随即将显示“设备配置详细信息”页：

    [![IoT 设备配置详细信息](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. 单击“下载配置文件”：

    [![下载配置文件](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. 将 JSON 文件另存为名为 configuration.json 的本地文件。

现在，有一个包含自动管理配置的文件。 在下一部分，将此配置作为包导入到远程监视解决方案中。

## <a name="import-a-package"></a>导入包

按照以下步骤将自动设备管理配置作为包导入到解决方案：

1. 导航到远程监视 Web UI 中的“包”页面：![包页面](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. 单击“+ 新建包”，选择“配置”作为包类型，然后单击“浏览”以选择在上一部分中保存的 configuration.json 文件：

    ![选择“配置”](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. 单击“上载”将包添加到远程监视解决方案中：

    ![上载的包](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

现在已经下载自动设备管理配置作为包。 在“部署”页面上，可以将此包部署到已连接的设备。

## <a name="next-steps"></a>后续步骤

现在已经了解了如何创建配置包并将其导入远程监视解决方案，下一步是了解如何[批量管理连接到远程监视的设备](iot-accelerators-remote-monitoring-bulk-configuration-update.md)。
