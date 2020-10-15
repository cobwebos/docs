---
title: 在 Azure Defender 中部署用于 IoT 的管理控制台
description: 了解如何在 Azure Defender 中部署用于 IoT 的管理控制台。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094265"
---
# <a name="deploy-the-management-console"></a>部署管理控制台
本文介绍如何为 IoT 本地管理控制台部署 Azure Defender。

## <a name="the-on-premises-management-console"></a>本地管理控制台

本地管理控制台提供所有网络资产的综合视图，并实时查看关键 IoT，并提供跨所有设备的风险指标和警报的实时视图。 它与 SOC 工作流紧密集成并运行书籍，实现了对缓解活动的优先级和与威胁的跨站点关联。

- 整体-通过用于资产管理、风险和漏洞管理的单个统一平台以及与事件响应的威胁监视，降低复杂性。

- 聚合和关联–显示、聚合和分析从所有站点收集的数据和警报。

- 控制所有传感器–从单个位置配置和监视所有传感器。

   ![用于 IoT 的 Azure Defender 站点管理视图](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>部署本地管理控制台设备

此过程需要获取自己的硬件和安装软件。 解决方案在经过认证的设备上运行。 购买认证设备时，请参考 [Azure Defender For IoT 硬件规范指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 。

有关下载 ISO 映像和安装传感器软件的详细信息，请参阅 [Azure Defender For IoT 安装指南](https://aka.ms/AzureDefenderforIoTInstallSensorISO) 。

**部署本地管理控制台：**

1. 导航到 IoT Microsoft Azure Defender。

2. 选择 **"本地管理控制台"**。

   ![用于 IoT 本地管理控制台视图的 Azure Defender](media/updates/image15.png)

3. 从 " **选择版本** " 菜单中选择版本3.1。

4. 选择 " **下载** " 并保存文件。

5. 安装软件后，请执行网络设置任务。 有关详细信息，请参阅 [Azure Defender IoT 网络安装指南](https://aka.ms/AzureDefenderForIoTNetworkSetup) 。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请转到模块配置的操作方法指南。
> [!div class="nextstepaction"]
> [模块配置操作方法指南](./how-to-agent-configuration.md)
