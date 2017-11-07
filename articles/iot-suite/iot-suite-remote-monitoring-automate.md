---
title: "在远程监视解决方案中检测设备问题 - Azure | Microsoft Docs"
description: "本教程介绍如何使用规则和操作在远程监视解决方案中自动检测基于阈值的设备问题。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2f2b221f5739ac370e110d60ed7812ce9ea5e05f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>使用基于阈值的规则检测问题

本教程介绍远程监视解决方案中的规则引擎的功能。 为了介绍这些功能，本教程在 Contoso IoT 应用程序中使用了一个方案。

Contoso 的某个规则可在**冷却器**设备报告的压力超过 250 PSI 时生成关键警报。 操作员希望能够通过查找初始压力峰值，来识别可能出现传感器问题的**冷却器**设备。 为了识别这些设备，我们将创建一个规则，以便在压力超过 150 PSI 时生成警告。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 在解决方案中查看规则
> * 创建新规则
> * 编辑现有规则
> * 删除规则

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)教程。

## <a name="view-the-rules-in-your-solution"></a>在解决方案中查看规则

解决方案中的“规则和操作”页显示所有当前规则的列表：

![“规则和操作”页](media/iot-suite-remote-monitoring-automate/rulesactions.png)

如果只想查看应用于**冷却器**设备的规则，请应用筛选器：

![筛选规则列表](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

在列表中选择某个规则后，可以查看其详细信息并对其进行编辑：

![查看规则详细信息](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

若要禁用、启用或删除一个或多个规则，请在列表中选择多个规则：

![选择多个规则](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>创建新规则

若要添加一个可在**冷却器**设备中的压力超过 150 PSI 时生成警告的新规则，请选择“新建规则”：

![创建规则](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

使用以下值创建规则：

| 设置          | 值                                 |
| ---------------- | ------------------------------------- |
| 名称             | 冷却器警告                       |
| 源           | **冷却器**设备组              |
| 触发器字段    | 压力                              |
| 触发器运算符 | 大于                          |
| 触发器值    | 150                                   |
| 严重性级别   | 警告                               |
| 警报事件文本 | 冷却器压力超过 150 PSI |

若要保存新规则，请选择“应用”。

可以在“规则和操作”页或“仪表板”页上查看规则的触发时间。

## <a name="edit-an-existing-rule"></a>编辑现有规则

若要对某个现有规则进行更改，请在规则列表中选择该规则。 然后，在“规则详细信息”面板中选择“编辑模式”。

![编辑规则](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>禁用规则

若要暂时关闭某个规则，可以在规则列表中禁用该规则。 选择要禁用的规则，再选择“禁用”。 列表中规则的“状态”会更改，指示该规则现已禁用。 可以使用相同的过程重新启用先前已禁用的规则。

![禁用规则](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

如果在列表中选择了多个规则，则可以同时启用和禁用多个规则。

## <a name="delete-a-rule"></a>删除规则

若要永久删除某个规则，请在规则列表中选择该规则，再选择“删除”。

如果在列表中选择了多个规则，则可以同时删除多个规则。

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 在解决方案中查看规则
> * 创建新规则
> * 编辑现有规则
> * 删除规则

了解如何使用基于阈值的规则检测问题后，我们建议接下来了解以下操作：

* [管理和配置设备](./iot-suite-remote-monitoring-manage.md)。
* [排查和修正设备问题](./iot-suite-remote-monitoring-maintain.md)。
* [使用模拟设备测试解决方案](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->