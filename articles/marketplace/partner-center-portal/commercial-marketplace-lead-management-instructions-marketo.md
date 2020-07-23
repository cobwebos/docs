---
title: Marketo 中的潜在顾客管理 - Microsoft 商业市场
description: 了解如何使用 Marketo CRM 系统管理来自 Microsoft AppSource 和 Azure 市场的潜在顾客。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: f1db7cc34a77c342a3d11e4b509d45a745a200db
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849165"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>使用 Marketo 管理商业市场潜在顾客

本文介绍如何设置 Marketo CRM 系统，以便处理来自 Microsoft AppSource 和 Azure 市场中产品/服务的潜在销售顾客。

## <a name="set-up-your-marketo-crm-system"></a>设置 Marketo CRM 系统

1. 登录到 Marketo。

1. 选择“Design Studio”。

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  选择“新建窗体”。

    ![Marketo 新建窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  在“新建窗体”对话框中填写必填字段，然后选择“创建”。 

    ![Marketo 新建窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  在“字段详细信息”页中，选择“完成”。 

    ![Marketo 完成窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  批准并关闭。

1. 在“MarketplaceLeadBackend”选项卡上，选择“嵌入代码”。  

    ![Marketo 嵌入代码](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo 的“嵌入代码”将显示类似于以下示例的代码。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. 复制“嵌入代码”窗体中显示的以下字段的值。 在下一步中，将使用这些值配置产品/服务以接收潜在顾客。 使用下面的示例说明如何从 Marketo 的嵌入代码示例获取所需的 ID。

    - 服务器 ID = ys12
    - Munchkin ID = 123-PQR-789
    - 窗体 ID = 1179

    找出这些值的另一种方法是：

    - 在 Marketo 实例的 URL 中找到服务器 ID，例如 `serverID.marketo.com`。
    - 转到“管理员” ** > ** “Munchkin 帐户 ID”字段中的“Munchkin” 菜单，或从 Marketo REST API 主机子域的第一个部分获取订阅的 Munchkin ID：`https://{Munchkin ID}.mktorest.com`。
    - 窗体 ID 是在步骤 7 中创建的“嵌入代码”窗体的 ID，用于从市场路由潜在顾客。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>配置你的产品/服务以将潜在顾客发送到 Marketo

准备好在发布门户中为你的产品/服务配置潜在顾客管理信息后，执行以下步骤。 

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 选择你的产品/服务，然后转到“产品/服务设置”选项卡。

1. 在“潜在顾客”部分下，选择“连接”。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="潜在顾客":::

1. 在“连接详细信息”弹出窗口中，为“潜在顾客目标”选择“Marketo”。

    ![选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. 提供“服务器 ID”、“Munchkin 帐户 ID”和“窗体 ID”。

    > [!NOTE]
    > 必须先完成产品/服务的其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。 

1. 在“联系人电子邮件”下，为公司中应在收到新潜在顾客时收到电子邮件通知的人员输入电子邮件地址。 可以提供多个电子邮件地址，用分号分隔。

1. 选择“确定”。

   若要确保已成功连接到潜在顾客目标，请选择“验证”。 如果成功，则在潜在顾客目标中会出现测试潜在顾客。

   ![连接详细信息弹出窗口](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
