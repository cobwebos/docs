---
title: 在 Marketo 中配置潜在客户管理 |Azure 应用商店
description: 为 Azure 市场客户配置 Marketo 的潜在客户管理。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 90f42954fd4d7324a7684795fca6ec302411790c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731116"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中配置潜在顾客管理

本文介绍如何设置 Marketo CRM 系统来处理市场产品/服务的销售线索。

## <a name="set-up-your-marketo-crm-system"></a>设置市场 CRM 系统

1. 登录到 Marketo。

2. 选择“Design Studio”。****

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  选择“新建窗体”。****

    ![Marketo 新建窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  在“新建窗体”中填写必填字段，然后选择“创建”。****

    ![Marketo 创建新窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  在“字段详细信息”中，选择“完成”。****

    ![Marketo 完成窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  同意并关闭。

7. 在 *"市场线索支持"* 选项卡上，选择 **"嵌入代码**"。 

    ![嵌入代码](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo 的“嵌入代码”将显示类似于以下示例的代码。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 复制嵌入代码窗体中显示的以下字段的值。 您将使用这些值将产品/服务配置为在下一步中接收潜在顾客。 使用下面的示例说明如何从 Marketo 的嵌入代码示例获取所需的 ID。

    - 服务器 ID = **ys12**
    - 蒙奇金 ID = **123-PQR-789**
    - 表格 ID = **1179**

    **找出这些值的另一种方法**

    - 服务器 ID 在 Marketo 实例的 URL 中找到，例如`serverID.marketo.com`""。
    - 通过"蒙奇金帐户 ID"字段中的管理员>Munchkin 菜单，或从 Marketo REST API 主机子域的第一部分获取订阅的蒙廷 ID： `https://{Munchkin ID}.mktorest.com`。
    - 表单 ID 是您在步骤 7 中创建的嵌入代码表单的 ID，用于从我们的市场路由潜在顾客。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>将您的优惠配置为向 Marketo 发送潜在顾客

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作： 

1. 导航到产品 **/服务"产品/服务"设置**页面。

1. 在"潜在顾客管理"部分下选择 **"连接**"。 

    ![铅管理 - 连接](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在"连接详细信息"弹出窗口上，为潜在顾客目标选择 **"市场市场**"。

    ![选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 提供**服务器 ID**、**蒙奇帐户 ID**和窗体**ID**。

    > [!Note]
    > 您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。 

5. **联系电子邮件**- 为公司中应接收新潜在顾客的电子邮件通知的人员提供电子邮件。 您可以通过用分号分隔它们来提供多封电子邮件。

6. 选择“确定”  。

   要确保已成功连接到潜在顾客目标，请单击验证按钮。 如果成功，您将在潜在顾客目标中具有测试潜在顾客。

   ![连接详细信息](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
