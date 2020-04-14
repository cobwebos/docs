---
title: 配置销售队伍的潜在客户管理 |Azure 应用商店
description: 为 Azure 应用商店客户配置销售队伍上的潜在顾客管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262971"
---
# <a name="configure-lead-management-for-salesforce"></a>为 Salesforce 配置潜在顾客管理

本文介绍如何设置 Salesforce 系统来处理来自商业市场产品/服务的销售线索。

> [!Note]
> 应用商店不支持预填充列表，例如 **"国家/地区"** 字段的值列表。 在继续之前，请确保没有设置任何列表。 或者，可以将[HTTPS 终结点](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)配置为接收潜在顾客。

## <a name="set-up-your-salesforce-system"></a>设置销售队伍系统

1. 登录到 Salesforce。
2. 如果您使用的是 Salesforce 照明体验。
    1. 从 Salesforce 主页中选择 **"设置**"。
    ![Salesforce 设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 从"设置"页面，通过左侧导航导航到**平台工具->功能设置->营销->网络到潜在顾客**。

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. 如果您使用的是 Salesforce 经典体验：
    1. 从 Salesforce 主页中选择 **"设置**"。
    ![Salesforce 经典设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 从"设置"页面，通过左侧导航导航到**生成->自定义>潜在顾客>Web 到潜在顾客**。

        ![Salesforce 经典 Web 到领导](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

其余的说明是相同的，无论您使用的 Salesforce 体验如何。

4. 在 **"Web 到潜在顾客设置"页上**，选择"**创建 Web 到潜在顾客窗体"** 按钮。
5. 在“Web-to-Lead 设置”**** 中，选择“创建 Web-to-Lead 窗体”****。
    ![销售队伍 - 网络到线索设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 在 **"创建 Web 到潜在顾客窗体**"窗体上`the Include reCAPTCHA in HTML`，请确保未选中设置，然后选择"**生成**"。 
    ![销售队伍 - 创建网络到线索表单](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 将显示一些 HTML 文本。 搜索文本"oid"并从 HTML 文本复制**oid 值**（仅在引号之间的文本），并保存它。 您将在发布门户上的 **"组织标识符"** 字段中粘贴此值。
    ![销售队伍 - 创建网络到线索表单](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. 所选**已完成**。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>将产品/服务配置为向 Salesforce 发送潜在顾客

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作：

1. 导航到产品 **/服务"产品/服务"设置**页面。
1. 在"潜在顾客管理"部分下选择 **"连接**"。
    ![铅管理 - 连接](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在"连接详细信息"弹出窗口上，选择 **"潜在顾客目标****"的 Salesforce，** 然后`oid`按照前面的步骤粘贴到 **"组织"标识符**字段，从 Web 到潜在顾客窗体。

1. **联系电子邮件**- 为公司中应接收新潜在顾客的电子邮件通知的人员提供电子邮件。 您可以通过用分号分隔它们来提供多封电子邮件。

1. 选择 **"确定**"。

要确保已成功连接到潜在顾客目标，请单击验证按钮。 如果成功，您将在潜在顾客目标中具有测试潜在顾客。

>[!Note]
>您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。

![连接详细信息 - 选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![连接详细信息 - 选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
