---
title: 配置规则并管理警报
description: 描述如何在服务器场Beats中配置规则和管理警报
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482980"
---
# <a name="configure-rules-and-manage-alerts"></a>配置规则并管理警报

Azure FarmBeats 允许您根据业务逻辑创建规则，此外还允许您从服务器场中部署的传感器和设备流动的传感器数据。 每当传感器值超过阈值时，规则都会在系统中触发警报。 通过查看和分析阈值后创建的警报，您可以快速对任何问题采取行动并获得所需的解决方案。

## <a name="create-rule"></a>创建规则

1. 在主页上，转到**规则**。
2. 选择**新规则**。 将显示"新规则"窗口。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 输入**规则名称**和**规则描述**，然后从 **"选择服务器场"** 下拉菜单中选择服务器场。
4. 键入服务器场名称以选择同一窗口中显示的服务器场和 **"条件"** 部分。  

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. 在 **"条件"** 中，输入**度量值**、**运算符**和**值**的值。
6. 在 **"度量值**"下拉菜单中键入度量值名称。
7. 选择 **"添加条件**"以向规则添加更多条件。
8. 选择**严重性级别**。
9. 在 **"操作"中**，打开**启用电子邮件**的切换按钮以启用电子邮件警报。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 输入要向其发送电子邮件提醒的**电子邮件地址**，以及 **"电子邮件主题**"和 **"附加说明**"。  
11. 在 **"规则状态**"中，打开 **"已启用**"切换按钮以启用或禁用规则。
    您可以查看受规则影响的设备数。
12. 选择 **"应用"** 以创建规则。

## <a name="view-rule"></a>查看值

"**服务器场"** 页显示可用规则的列表。 选择**规则名称**。 窗口显示适用于所选规则的以下详细信息：
 - 规则名称
 - 链接到规则关联的服务器场
 - 创建日期
 - 上次更新日期
 - 严重性级别
 - 规则状态
 - 条件列表  
 - 受规则影响的设备数

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>编辑规则

要编辑规则，请按照以下步骤操作：

1. 在主页上，从左侧导航菜单中选择 **"规则**"。
   将显示规则窗口。
2. 选择要为其编辑的规则。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 从操作栏中选择 **"编辑**"，"**编辑规则"** 窗口将显示。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 更改**规则名称**和**规则说明**，然后从 **"选择服务器场"** 下拉菜单中选择服务器场。
5. 键入服务器场名称以选择**服务器场，条件**将显示在同一窗口中。  
6. 在 **"条件**"中，编辑**度量值**、**运算符**和**值**。
7. 在 **"度量值**"下拉菜单中键入度量值名称。
8. 选择 **"添加条件**"以向规则添加/编辑条件。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  选择**严重性级别**。  
10. 在 **"操作"中**，打开**启用电子邮件**的切换按钮以启用电子邮件警报。
11. 编辑要发送电子邮件提醒的**电子邮件地址**，以及 **"电子邮件主题**"和 **"附加说明**"。  
12. 在 **"规则状态**"中，打开 **"已启用**"切换按钮以启用或禁用规则。
您可以查看受此规则影响的设备数。
13. 选择 **"应用"** 以编辑规则。

## <a name="change-rule-status"></a>更改规则状态

要更改规则的状态，请按照以下步骤操作：

1. 在主页上，从左侧导航菜单中选择 **"规则**"。 将显示规则窗口。
2. 选择要为其更改状态的规则。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 从操作栏中选择 **"更改状态**"。 将显示 **"更改状态"** 窗口。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. 使用 **"更改状态**"切换按钮更改规则状态。
   您可以查看受规则影响的设备数。
4. 选择 **"应用"** 以更改规则状态。

## <a name="delete-rule"></a>删除规则

要删除规则，请按照以下步骤操作：

1. 在主页上，从左侧导航菜单中选择 **"规则**"。 将显示规则窗口。
2. 选择要为其删除的规则。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 从操作栏中选择 **"删除**"。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. 将显示"**删除规则**"对话框。 选择 **"删除**"。
