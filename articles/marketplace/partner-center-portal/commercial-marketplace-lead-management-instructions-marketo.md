---
title: 在 Marketo 中配置潜在客户管理 |Azure Marketplace
description: 为 Azure marketplace 客户配置 Marketo 的潜在客户管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 9470a593f5db564689a0b26601a201874c215aa2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902086"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中配置潜在顾客管理

本文介绍如何设置 Marketo CRM 系统, 以处理 marketplace 产品/服务的销售潜在顾客。

## <a name="set-up-your-marketo-crm-system"></a>设置 Marketo CRM 系统

1. 登录到 Marketo。
2. 选择“Design Studio”。
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  选择“新建窗体”。
    ![Marketo 新建窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  在“新建窗体”中填写必填字段，然后选择“创建”。
    ![Marketo 创建新窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  在“字段详细信息”中，选择“完成”。
    ![Marketo 完成窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  同意并关闭。

7. 在 " *MarketplaceLeadBacked* " 选项卡上, 选择 "**嵌入代码**"。 

    ![嵌入代码](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo 的“嵌入代码”将显示类似于以下示例的代码。

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 复制嵌入代码窗体中显示的以下字段的值。 在下一步中, 你将使用这些值来配置你的产品/服务。 使用下面的示例说明如何从 Marketo 的嵌入代码示例获取所需的 ID。

    - 服务器 ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 表单 ID = **1179**

    **计算这些值的另一种方法**

    - 在 Marketo 实例的 URL 中找到服务器 ID, 例如 "`serverID.marketo.com`"。
    - 转到 "Munchkin 帐户 ID" 字段中的 "管理员" > Munchkin "菜单, 或从 Marketo REST API host 子域的第一部分获取订阅的 Munching ID: `https://{Munchkin ID}.mktorest.com`。
    - 表单 ID 是在步骤7中创建的嵌入代码窗体的 ID, 用于从我们的 marketplace 路由潜在顾客。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>配置你的产品/服务以将潜在顾客发送到 Marketo

准备好在发布门户中为产品/服务配置潜在顾客管理信息时, 请执行以下步骤: 

1. 导航到产品/服务的 "**产品/服务设置**" 页。
1. 选择 "潜在顾客管理" 部分下的 "**连接**"。 

    ![潜在客户管理-连接](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在 "连接详细信息" 弹出窗口中, 选择 " **Marketo** " 作为 "潜在顾客" 目标。

    ![选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 提供**服务器 id**、 **Munching 帐户 ID**和**表单 id**。

    >[!Note]
    >您必须先完成该产品/服务的其余部分的配置, 然后发布该产品/服务, 才能接收该产品/服务的潜在客户。 

