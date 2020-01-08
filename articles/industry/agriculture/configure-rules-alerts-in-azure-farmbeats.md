---
title: 配置规则并管理警报
description: 介绍如何在 FarmBeats 中配置规则和管理警报
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482980"
---
# <a name="configure-rules-and-manage-alerts"></a>配置规则并管理警报

借助 Azure FarmBeats，你可以根据业务逻辑创建规则，以及从你的场中部署的传感器和设备流向的传感器数据。 当传感器值超过阈值时，规则将触发系统中的警报。 通过查看和分析在阈值之后创建的警报，可以快速处理任何问题并获取所需的解决方案。

## <a name="create-rule"></a>创建规则

1. 在主页上，请参阅 "**规则**"。
2. 选择 "**新建规则**"。 将显示 "新建规则" 窗口。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 输入**规则名称**和**规则说明**，然后从 "**选择场**" 下拉菜单中选择场。
4. 键入场名称以选择 "场和**条件**" 部分显示在同一窗口中。  

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. 在 "**条件**" 中，输入 "**度量**值"、"**运算符**" 和 "**值**" 值。
6. 在 "**度量值**" 下拉菜单中键入度量值名称。
7. 选择 " **+ 添加条件**"，将更多条件添加到规则。
8. 选择**严重性级别**。
9. 在 "**操作**" 中，切换到 "**已启用电子邮件**" 切换按钮以启用电子邮件警报。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 输入要向其发送电子邮件警报的**电子邮件地址**，以及**电子邮件主题**和**其他说明**。  
11. 在 "**规则状态**" 中，切换到 "**已启用**" 切换按钮以启用或禁用规则。
    你可以查看将受规则影响的设备的数量。
12. 选择 "**应用**" 以创建规则。

## <a name="view-rule"></a>查看值

"**场**" 页显示可用规则的列表。 选择**规则名称**。 窗口将显示适用于所选规则的以下详细信息：
 - 规则名称
 - 与规则关联的场的链接
 - 创建日期
 - 上次更新日期
 - 严重性级别
 - 规则状态
 - 条件列表  
 - 受规则影响的设备数

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>编辑规则

若要编辑规则，请执行以下步骤：

1. 在主页上的左侧导航菜单中，选择 "**规则**"。
   将显示 "规则" 窗口。
2. 选择要编辑的规则。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 从操作栏中选择 "**编辑**"，将显示 "**编辑规则**" 窗口。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 更改**规则名称**和**规则说明**，然后从 "**选择场**" 下拉菜单中选择场。
5. 键入场名称以选择场，并在同一窗口中显示**条件**。  
6. 在 "**条件**" 中，编辑**度量**值、**运算符**和**值**。
7. 在 "**度量值**" 下拉菜单中键入度量值名称。
8. 选择 " **+ 添加条件**" 可向规则中添加/编辑条件。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  选择**严重性级别**。  
10. 在 "**操作**" 中，切换到 "**已启用电子邮件**" 切换按钮以启用电子邮件警报。
11. 编辑要发送电子邮件警报的**电子邮件地址**，以及**电子邮件主题**和**其他说明**。  
12. 在 "**规则状态**" 中，切换到 "**已启用**" 切换按钮以启用或禁用规则。
你可以查看将受此规则影响的设备的数量。
13. 选择 "**应用**" 以编辑规则。

## <a name="change-rule-status"></a>更改规则状态

若要更改规则的状态，请执行以下步骤：

1. 在主页上的左侧导航菜单中，选择 "**规则**"。 将显示 "规则" 窗口。
2. 选择要更改其状态的规则。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 从操作栏中选择 "**更改状态**"。 将显示 "**更改状态**" 窗口。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. 使用 "**更改状态**" 切换按钮更改规则状态。
   你可以查看将受规则影响的设备的数量。
4. 选择 "**应用**" 以更改规则状态。

## <a name="delete-rule"></a>删除规则

若要删除规则，请执行以下步骤：

1. 在主页上的左侧导航菜单中，选择 "**规则**"。 将显示 "规则" 窗口。
2. 选择要删除的规则。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 从操作栏中选择 "**删除**"。

    ![项目场节拍](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. 将显示 "**删除规则**" 对话框。 选择“删除”。
