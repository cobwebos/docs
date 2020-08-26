---
title: 使用 Azure IoT Central 创建持续患者监视应用 | Microsoft Docs
description: 了解如何使用 Azure IoT Central 应用程序模板生成持续患者监视应用程序。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 704c56745ad89e9ed2f79e8a863f1d0bc9845bf9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001819"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>教程：部署和演练持续患者监视应用模板



本教程向解决方案构建人员介绍如何通过部署 IoT Central 持续患者监视应用程序模板开始入门。 你将了解如何部署模板、有哪些现成的内容以及接下来要做什么。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建应用程序模板
> * 演练应用程序模板

## <a name="create-an-application-template"></a>创建应用程序模板

导航到 [Azure IoT Central 应用程序管理器网站](https://apps.azureiotcentral.com/)。 从左侧导航栏中选择“生成”，然后单击“医疗保健”选项卡   。 

>[!div class="mx-imgBorder"] 
>![应用管理器医疗保健](media/app-manager-health.png)

单击“创建应用”按钮开始创建应用程序，然后使用 Microsoft 个人、工作或学校帐户登录  。 这会转到“新建应用程序”  页。

![创建应用程序医疗保健](media/app-manager-health-create.png)

![创建应用程序医疗保健计费信息](media/app-manager-health-create-billinginfo.png)

创建应用程序：

1. Azure IoT Central 会根据所选模板自动建议应用程序名称。 你可以接受此名称或输入自己的友好应用程序名称，例如“持续患者监视”  。 Azure IoT Central 还会根据应用程序名称为你生成唯一的 URL 前缀。 如果你愿意，可以自由地将此 URL 前缀更改为更令人难忘的内容。

2. 你可以选择是使用“免费”  定价计划还是使用“标准”  定价计划之一创建应用程序。 使用免费计划创建的应用程序免费 7 天，然后过期，最多可在 5 台设备上免费使用。 你可以在应用程序到期前随时将其从免费计划移动到标准定价计划。 如果选择了免费计划，则需要输入联系信息并选择是否接收 Microsoft 发送的信息和使用技巧。 使用标准计划创建的应用程序支持最多两个免费设备，并且要求你输入 Azure 订阅信息以便计费。

3. 选择页面底部的“创建”以部署应用程序  。

## <a name="walk-through-the-application-template"></a>演练应用程序模板

### <a name="dashboards"></a>仪表板

部署应用模板之后，将首先登陆“Lamna 住院患者监视仪表板”  。 Lamna 医疗保健是一个虚构的医院系统，其中包含两个医院：Woodgrove 医院和 Burkville 医院。 在 Woodgrove 医院的操作员仪表板上，你将看到此模板中与设备有关的信息和遥测数据，以及可执行的一组命令、作业和操作。 在仪表板中，你可以执行以下操作：

* 查看设备遥测数据和属性，例如设备的电池电量或其连接状态   。

* 查看 Smart Vitals Patch 设备的平面布置图和位置  。

* 为新患者重新预配 Smart Vitals Patch  。

* 请参阅医院护理团队为跟踪其患者而可能查看的提供程序仪表板的示例  。

* 更改设备的患者状态以指示该设备是用于住院患者方案还是远程方案  。

>[!div class="mx-imgBorder"] 
>![Lamna 住院患者](media/lamna-in-patient.png)

你还可以单击“转到远程患者仪表板”查看用于 Burkville 医院的第二个操作员仪表板  。 此仪表板包含一组类似的操作、遥测和信息。 此外，还可以看到正在使用的多个设备，且能够更新每台设备上的固件  。

>[!div class="mx-imgBorder"] 
>![Lamna 远程](media/lamna-remote.png)

在这两个仪表板上，你始终可以链接回此文档。

### <a name="device-templates"></a>设备模板

如果单击“设备模板”选项卡，则会看到模板中包含两种不同的设备类型  ：

* **Smart Vitals Patch**：此设备提供一个修补程序，用以测量不同类型的生命体征。 它可用于监视医院内外的患者。 如果单击模板，除了发送设备数据（如电池电量和设备温度）外，修补程序还会发送患者健康状况数据（如呼吸频率和血压）。

* **Smart Knee Brace**：此设备表示患者在从膝关节置换手术中恢复过来时可能使用的膝盖支架。 如果单击此模板，除了设备数据外，还将看到诸如运动范围和加速之类的功能。

>[!div class="mx-imgBorder"] 
>![Smart Vitals Patch 设备模板](media/smart-vitals-device-template.png)

### <a name="device-groups"></a>设备组 
通过设备组，可以对一组设备进行逻辑分组，然后还可以对它们执行批量查询或各种操作。 

单击“设备组”选项卡时，你将看到我们已为应用程序中的各个设备模板创建了一些默认设备组。 你还会发现，我们已创建了其他两个示例设备组，名称分别是“预配设备”和“包含过期固件的设备”。 我们将使用这些示例设备组作为输入，来运行一些[作业](#jobs)。

### <a name="rules"></a>规则

跳转到“规则”选项卡时，将看到应用程序模板中存在的三个规则：

* **温度过高**：当 Smart Knee Brace 的设备温度超过 95&deg;F 且持续时间超过 5 分钟窗口期时，将触发此规则。 可使用此规则提醒患者和护理团队，并远程冷却设备。

* **检测到跌倒**：如果检测到患者跌倒，则触发此规则。 可使用此规则配置操作，以部署操作团队来帮助跌倒的患者。

* **Patch 电量不足**：当设备上的电池电量低于 10% 时将触发此规则。 可使用此规则来触发通知，通知患者为其设备充电。

>[!div class="mx-imgBorder"] 
>![Brace 温度过高规则](media/brace-temp-rule.png)

### <a name="jobs"></a>作业

通过作业，可以通过将[设备组](#device-groups)用作输入来对一组设备运行批量操作。 我们已为应用程序模板设定种子，其中包含两个示例作业，解决方案操作员可能需要在设备生命周期的某个阶段运行这两个作业：
* 更新膝盖支架固件：此作业将在设备组“包含过期固件的设备”中查找设备，并运行命令以将这些设备更新到膝盖支架的最新固件版本。 此示例作业假设设备能够接收“更新”命令并且能够直接从云提取固件文件。  

* 重新预配设备：如果有一组设备最近返回到医院，并且需要为下一批患者进行重新预配，则可以运行此作业，以便批量更新预配设备。 在这种情况下，我们将提取“预配设备”设备组中的所有设备，并执行命令来“重新预配”它们。 

### <a name="devices"></a>设备

单击“设备”选项卡，然后选择 Smart Knee Brace 的实例 。 你将看到有三个视图可用于浏览所选特定设备的相关信息。 在为你的设备生成设备模板时，会创建并发布这些视图，这意味着它们在你连接或模拟的所有设备中将保持一致。

“仪表板”视图提供了来自面向操作员的设备的遥测和属性的概述。

“属性”选项卡可用于编辑云属性以及读取/写入设备属性。

“命令”选项卡可用于运行已建模为设备模板一部分的命令。

>[!div class="mx-imgBorder"] 
>![膝盖支架视图](media/knee-brace-dashboard.png)

### <a name="data-export"></a>数据导出

通过数据导出，可以将 IoT Central 设备数据连续导出到其他 Azure 设备，其中包括 [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请依次访问“管理”>“应用程序设置”，然后单击“删除”来删除该应用程序 。

>[!div class="mx-imgBorder"] 
>![删除应用](media/admin-delete.png)

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何创建连接到 IoT Central 应用程序的提供程序仪表板。

> [!div class="nextstepaction"]
> [生成提供程序仪表板](howto-health-data-triage.md)