---
title: 在远程监视解决方案中检测设备问题 - Azure | Microsoft Docs
description: 本教程介绍如何使用规则和操作在远程监视解决方案中自动检测基于阈值的设备问题。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f3583b27b2fb9959e65a9c66a75c1174ebf3e238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>使用基于阈值的规则检测问题

本教程介绍远程监视解决方案中的规则引擎的功能。 为了介绍这些功能，本教程在 Contoso IoT 应用程序中使用了一个方案。

Contoso 的某个规则可在**冷却器**设备报告的压力超过 250 PSI 时生成关键警报。 操作员希望能够通过查找初始压力峰值，来识别可能出现传感器问题的**冷却器**设备。 为了识别这些设备，我们将创建一个规则，以便在压力超过 150 PSI 时生成警告。

而且已经告知你，当**冷却器**设备在过去 5 分钟内的平均湿度大于 80% 且**冷却器**设备在过去 5 分钟内的温度高于 75 华氏度时，需要触发一个严重警报。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 在解决方案中查看规则
> * 创建新规则
> * 创建具有多个条件的新规则
> * 编辑现有规则
> * 删除规则

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)教程。

## <a name="view-the-rules-in-your-solution"></a>在解决方案中查看规则

解决方案中的“规则”页显示所有当前规则的列表：

![“规则和操作”页](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

如果只想查看应用于**冷却器**设备的规则，请应用筛选器：

![筛选规则列表](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

在列表中选择某个规则后，可以查看其详细信息并对其进行编辑：

![查看规则详细信息](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

若要禁用、启用或删除一个或多个规则，请在列表中选择多个规则：

![选择多个规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>创建新规则

若要添加一个可在**冷却器**设备中的压力超过 150 PSI 时生成警告的新规则，请选择“新建规则”：

![创建规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

使用以下值创建规则：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 规则名称        | 冷却器警告                       |
| 说明      | 冷却器压力超过 150 PSI |
| 设备组     | “冷却器”设备组             |
| 计算      | 即时                               |
| 条件 1 字段| 压强                              |
| 条件 1 运算符 | 大于                      |
| 条件 1 值    | 150                               |
| 严重性级别  | 警告                               |

若要保存新规则，请选择“应用”。

可以在“规则”页或“仪表板”页上查看规则的触发时间。

## <a name="create-a-new-rule-with-multiple-conditions"></a>创建具有多个条件的新规则

若要创建具有多个条件的新规则，使其在**冷却器**设备在过去 5 分钟内的平均湿度大于 80% 且**冷却器**设备在过去 5 分钟内的温度高于 75 华氏度时生成一个严重警报，请选择“新建规则”：

![创建多条件规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

使用以下值创建规则：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 规则名称        | 冷却器湿度和温度严重    |
| 说明      | 湿度和温度都出现严重问题 |
| 设备组     | “冷却器”设备组             |
| 计算      | 平均值                               |
| 时间段      | 5                                     |
| 条件 1 字段| 湿度                              |
| 条件 1 运算符 | 大于                      |
| 条件 1 值    | 80                               |
| 严重性级别  | 严重                              |

若要添加第二个条件，请单击“+ 添加条件”。

![创建条件 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

在新条件中使用以下值：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 条件 2 字段| 温度                           |
| 条件 2 运算符 | 大于                      |
| 条件 2 值    | 75                                |

若要保存新规则，请选择“应用”。

可以在“规则”页或“仪表板”页上查看规则的触发时间。

## <a name="edit-an-existing-rule"></a>编辑现有规则

若要对某个现有规则进行更改，请在规则列表中选择该规则。

![编辑规则](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 在解决方案中查看规则
> * 创建新规则
> * 编辑现有规则
> * 删除规则

了解如何使用基于阈值的规则检测问题后，我们建议接下来了解以下操作：

* [管理和配置设备](iot-accelerators-remote-monitoring-manage.md)。
* [排查和修正设备问题](iot-accelerators-remote-monitoring-maintain.md)。
* [使用模拟设备测试解决方案](iot-accelerators-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->