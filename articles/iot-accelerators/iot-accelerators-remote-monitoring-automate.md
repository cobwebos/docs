---
title: 在基于 Azure 的远程监视解决方案中检测设备问题 | Microsoft Docs
description: 本教程介绍如何使用规则和操作在远程监视解决方案中自动检测基于阈值的设备问题。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098475"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>教程：检测连接到监视解决方案的设备问题

在本教程中，我们将配置远程监视解决方案加速器，以检测联网 IoT 设备的问题。 若要检测设备问题，请添加可在解决方案仪表板上生成警报的规则。

为了介绍规则和警报，本教程使用了一个模拟冷却器设备。 该冷却器由一家称作 Contoso 的组织进行管理，并已连接到远程监视解决方案加速器。 Contoso 已创建一个规则，当冷却器中的压力超过 298 PSI 时，该规则会生成严重警报。 Contoso 的操作员希望能够通过查找初始压力峰值，来识别可能出现传感器问题的冷却器设备。 若要识别此类设备，请添加一个规则，当冷却器器的压力超过 150 PSI 时，该规则会生成警告性警报。

此外，如果在过去 5 分钟内，设备中的平均平均湿度超过 80% 并且设备的温度超过 75 华氏度，则操作员必须针对冷却器创建严重警报。

本教程介绍以下操作：

>[!div class="checklist"]
> * 在解决方案中查看规则
> * 创建规则
> * 创建具有多个条件的规则
> * 编辑现有规则
> * 打开和关闭规则

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案加速器的实例。

如果尚未部署远程监视解决方案加速器，应完成[部署基于云的远程监视解决方案](quickstart-remote-monitoring-deploy.md)教程。

## <a name="view-the-existing-rules"></a>查看现有规则

解决方案加速器中的“规则”页显示所有当前规则的列表：

[![“规则”页](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

如果只想查看应用于冷却器设备的规则，请应用筛选器：

[![筛选规则列表](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

在列表中选择某个规则后，可以查看其详细信息并对其进行编辑：

[![查看规则详细信息](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

若要禁用或启用一个或多个规则，请在列表中选择一个或多个规则：

[![选择多个规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>创建规则

若要创建一个可在冷却器设备中的压力超过 150 PSI 时生成警告的规则，请单击“新建规则”。 使用以下值创建规则：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 规则名称        | 冷却器警告                       |
| 说明      | 冷却器压力超过 150 PSI |
| 设备组     | “冷却器”设备组             |
| 计算      | 即时                               |
| 条件 1 字段| 压力                              |
| 条件 1 运算符 | 大于                      |
| 条件 1 值    | 150                               |
| 严重性级别  | 警告                               |

[![创建警告规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

若要保存新规则，请单击“应用”。

可以在“规则”页或“仪表板”页上查看规则的触发时间：

[![触发的警告规则](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>创建具有多个条件的规则

若要创建一个可在过去 5 分钟内当冷却器设备中的平均平均湿度超过 80% 并且平均温度超过 75 华氏度时生成严重警报的、具有多个条件的规则，请单击“新建规则”。 使用以下值创建规则：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 规则名称        | 冷却器湿度和温度严重    |
| 说明      | 湿度和温度都出现严重问题 |
| 设备组     | “冷却器”设备组             |
| 计算      | 平均值                               |
| 时间段      | 5                                     |
| 条件 1 字段| 湿度                              |
| 条件 1 运算符 | 大于                      |
| 条件 1 值    | 80                                |
| 严重性级别  | 严重                              |

[![创建多条件规则 - 第一部分](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

若要添加第二个条件，请单击“+ 添加条件”。 为新条件使用以下值：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 条件 2 字段| 温度                           |
| 条件 2 运算符 | 大于                      |
| 条件 2 值    | 75                                |

[![创建多条件规则 - 第二部分](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

若要保存新规则，请单击“应用”。

可以在“规则”页或“仪表板”页上查看规则的触发时间：

[![触发的多条件规则](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>编辑现有规则

若要对某个现有规则进行更改，请在规则列表中选择该规则，然后单击“编辑”：

[![编辑规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>禁用规则

若要暂时关闭某个规则，可以在规则列表中禁用该规则。 选择要禁用的规则，然后选择“禁用”。 列表中规则的“状态”会更改，指示该规则现已禁用。 可以使用相同的过程重新启用先前已禁用的规则。

[![禁用规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

在列表中选择多个规则可以同时启用和禁用多个规则。

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>清理资源

如果你打算继续学习下一篇教程，请将远程监视解决方案加速器保持为部署状态。 如果不再使用解决方案加速器，可在设置面板中停止模拟设备，以减少运行解决方案加速器所产生的费用：

[![暂停遥测](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

准备好学习下一篇教程时，可以重启模拟设备。

如果不再需要该解决方案加速器，请从[预配的解决方案](https://www.azureiotsolutions.com/Accelerators#dashboard)页中将其删除：

![删除解决方案](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用远程监视解决方案加速器中的“规则”页来创建和管理可在解决方案中触发警报的规则。 若要了解如何使用解决方案加速器来管理和配置联网设备，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [配置并管理连接到监视解决方案的设备](iot-accelerators-remote-monitoring-manage.md)