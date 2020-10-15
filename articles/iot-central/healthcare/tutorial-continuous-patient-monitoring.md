---
title: 教程 - 使用 Azure IoT Central 创建持续患者监视应用 | Microsoft Docs
description: 在本教程中，你将了解如何使用 Azure IoT Central 应用程序模板生成持续患者监视应用程序。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 1967a2fb5adebe01ef4bff8d58f7832bffe95762
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531264"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>教程：部署和演练持续患者监视应用模板

本教程向解决方案构建人员介绍如何通过部署 IoT Central 持续患者监视应用程序模板开始入门。 了解如何部署和使用模板。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建应用程序模板
> * 演练应用程序模板

## <a name="create-an-application-template"></a>创建应用程序模板

导航到 [Azure IoT Central 应用程序管理器网站](https://apps.azureiotcentral.com/)。 从左侧导航栏中选择“生成”，然后选择“医疗保健”选项卡 。

:::image type="content" source="media/app-manager-health.png" alt-text="医疗保健应用模板":::

选择“创建应用”按钮开始创建应用程序，然后使用 Microsoft 个人、工作或学校帐户登录。 这会转到“新建应用程序”  页。

![创建应用程序医疗保健](media/app-manager-health-create.png)

![创建应用程序医疗保健计费信息](media/app-manager-health-create-billinginfo.png)

创建应用程序：

1. Azure IoT Central 会根据所选模板自动建议应用程序名称。 你可以接受此名称或输入自己的友好应用程序名称，例如“持续患者监视”  。 Azure IoT Central 还会根据应用程序名称为你生成唯一的 URL 前缀。 如果你愿意，可以自由地将此 URL 前缀更改为更令人难忘的内容。

2. 你可以选择是使用“免费”  定价计划还是使用“标准”  定价计划之一创建应用程序。 使用免费计划创建的应用程序免费 7 天，然后过期，最多可在 5 台设备上免费使用。 你可以在应用程序到期前随时将其从免费计划移动到标准定价计划。 如果选择了免费计划，则需要输入联系信息并选择是否接收 Microsoft 发送的信息和使用技巧。 使用标准计划创建的应用程序支持最多两个免费设备，并且要求你输入 Azure 订阅信息以便计费。

3. 选择页面底部的“创建”以部署应用程序  。

## <a name="walk-through-the-application-template"></a>演练应用程序模板

### <a name="dashboards"></a>仪表板

部署应用模板之后，将首先登陆“Lamna 住院患者监视仪表板”  。 Lamna 医疗保健是一个虚构的医院系统，其中包含两个医院：Woodgrove 医院和 Burkville 医院。 在 Woodgrove 医院操作员仪表板上，你可以：

* 查看设备遥测数据和属性，例如设备的电池电量或其连接状态   。

* 查看 Smart Vitals Patch 设备的平面布置图和位置  。

* 为新患者重新预配 Smart Vitals Patch  。

* 请参阅医院护理团队为跟踪其患者而可能查看的提供程序仪表板的示例  。

* 更改设备的患者状态以指示该设备是用于住院患者方案还是远程方案  。

:::image type="content" source="media/lamna-in-patient.png" alt-text="医疗保健应用模板":::

还可以选择“转到远程患者仪表板”，查看 Burkville 医院操作员仪表板。 此仪表板包含一组类似的操作、遥测和信息。 还可以查看正在使用的多个设备，并选择“更新每个设备上的固件”。

:::image type="content" source="media/lamna-remote.png" alt-text="医疗保健应用模板":::

### <a name="device-templates"></a>设备模板

如果选择“设备模板”，则会在模板中看到两个设备类型：

* **Smart Vitals Patch**：此设备表示测量各种生命体征的补丁。 它用于监视医院内外的患者。 如果选择模板，你会看到此补丁同时发送设备数据（如电池电量和设备温度）和患者健康状况数据（如呼吸频率和血压）。

* **Smart Knee Brace**：此设备表示患者在从膝关节置换手术中恢复过来时使用的膝盖支架。 如果选择此模板，则会看到设备数据、运动范围和加速等功能。

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="医疗保健应用模板":::

### <a name="device-groups"></a>设备组

使用设备组对一组设备进行逻辑分组，然后对其执行批量查询或操作。

如果选择“设备组”选项卡，则会在应用程序中看到每个设备模板的默认设备组。 还创建了另外两个示例设备组，名称分别是“预配设备”和“包含过期固件的设备” 。 可以将这些示例设备组用作输入来运行应用程序中的某些[作业](#jobs)。

### <a name="rules"></a>规则

如果选择“规则”，则会看到模板中的三个规则：

* **温度过高**：当 Smart Knee Brace 的设备温度超过 95&deg;F 且持续时间超过 5 分钟窗口期时，将触发此规则。 使用此规则来提醒患者和护理团队，并远程冷却设备。

* **检测到跌倒**：如果检测到患者跌倒，则触发此规则。 使用此规则来配置操作，以部署操作团队来帮助跌倒的患者。

* **Patch 电量不足**：当设备上的电池电量低于 10% 时将触发此规则。 使用此规则来触发通知，通知患者为其设备充电。

:::image type="content" source="media/brace-temp-rule.png" alt-text="医疗保健应用模板":::

### <a name="jobs"></a>作业

通过作业，你可以将[设备组](#device-groups)用作输入来对一组设备运行批量操作。 应用程序模板包含操作员可运行的两个示例作业：

* 更新膝盖支架固件：此作业在设备组“包含过期固件的设备”中查找设备，并运行命令以将这些设备更新到最新固件版本。 此示例作业假设设备能够处理“update”命令，然后从云中提取固件文件。  

* 重新预配设备：你有一组最近返回到医院的设备。 此作业在设备组“预配设备”中查找设备，并运行命令以为下一组患者重新预配它们。

### <a name="devices"></a>设备

选择“设备”选项卡，然后选择“智能膝盖支架”的实例 。 有三个视图可用于浏览所选特定设备的相关信息。 构建设备的设备模板时，会创建并发布这些视图。 因此，这些视图在你连接或模拟的所有设备中保持一致。

“仪表板”视图提供了设备的面向操作员的遥测和属性的概述。

通过“属性”选项卡，可以编辑云属性以及读取/写入设备属性。

通过“命令”选项卡，可以在设备上运行命令。

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="医疗保健应用模板":::

### <a name="data-export"></a>数据导出

通过数据导出，可以将设备数据连续导出到其他 Azure 服务，包括 [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请依次访问“管理”>“应用程序设置”，然后单击“删除”来删除该应用程序 。

:::image type="content" source="media/admin-delete.png" alt-text="医疗保健应用模板":::

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何创建连接到 IoT Central 应用程序的提供程序仪表板。

> [!div class="nextstepaction"]
> [生成提供程序仪表板](howto-health-data-triage.md)