---
title: Salesforce 中的潜在客户管理-Microsoft 商业市场
description: 了解如何使用 Salesforce 配置 Microsoft AppSource 和 Azure Marketplace 的潜在客户
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7d64b8914fa0b109dfc662a97a7f84d94e3491ec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789702"
---
# <a name="configure-lead-management-for-salesforce"></a>为 Salesforce 配置潜在顾客管理

本文介绍如何设置 Salesforce 系统，以便在 Microsoft AppSource 和 Azure Marketplace 中处理产品/服务的销售线索。

> [!NOTE]
> Azure Marketplace 不支持预填充列表，如 "**国家/地区**" 字段的值列表。 请确保在继续操作之前未设置任何列表。 或者，你可以配置[HTTPS 终结点](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)以接收潜在顾客。

## <a name="set-up-your-salesforce-system"></a>设置 Salesforce 系统

1. 登录到 Salesforce。
1. 导航到 " **Web 到主导**" 设置。 
    
    如果使用的是 Salesforce 照明体验
    1. 选择 Salesforce 主页上的 "**安装**"。

       ![Salesforce 设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 在 "**安装**" 页上，中转到 "**平台工具** > "**功能设置** > "**营销** > **Web 到潜在客户**"。

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    如果使用的是 Salesforce 经典体验：

    1. 选择 Salesforce 主页上的 "**安装**"。

       ![Salesforce 经典设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 在 "**设置**" 页上，选择 "**生成** > **自定义** > **潜在客户** > **"。**

        ![Salesforce 经典 Web 到主导](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   对于这两个 Salesforce 体验，剩余步骤是相同的。

1. 在 " **web 到客户设置**" 页上，选择 "**创建 Web 到主导窗体**" 按钮。
1. 在 " **web 到客户" 安装**中，选择 "**创建 Web 到潜在顾客" 窗体**。

    ![Salesforce Web 到主导设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. 在 "**创建 Web 到主导窗体**" 上，确保清除`Include reCAPTCHA in HTML`该设置，然后选择 "**生成**"。

    ![Salesforce 创建 Web 到主导窗体窗格](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. 你会看到一些 HTML 文本。 搜索文本 "oid" 并复制 HTML 文本中的 **"oid" 值**（仅在引号之间输入文本）并保存。 将此值粘贴到发布门户上的 "**组织标识符**" 字段。

    ![Salesforce 创建了一个显示 HTML "oid" 值的 Web 到主导窗体](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. 选择“已完成”****。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>配置你的产品/服务以将潜在客户发送到 Salesforce

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 选择产品/服务，并中转到 "**产品/服务" 设置**选项卡。

1. 在 "**潜在客户管理**" 部分下，选择 "**连接**"。 

    ![潜在客户管理部分连接按钮](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在 "**连接详细信息**" 弹出窗口中，选择 " **Salesforce** " 作为**潜在顾客目标**，并将所创建的 Web 到主导窗体中的`oid`值粘贴到 "**组织标识符**" 字段。

    !["连接详细信息" 弹出窗口 "验证联系人电子邮件" 框](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. 在 "**联系人电子邮件**" 下，输入公司中应在收到新潜在客户时接收电子邮件通知的人员的电子邮件地址。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”  。

若要确保已成功连接到潜在客户目标，请选择 "**验证**"。 如果成功，则在潜在客户目标中会出现测试主管。

>[!NOTE]
>您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。
