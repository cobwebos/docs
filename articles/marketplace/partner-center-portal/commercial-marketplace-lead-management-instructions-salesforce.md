---
title: Salesforce 中的潜在顾客管理 - Microsoft 商业市场
description: 了解如何使用 Salesforce 为 Microsoft AppSource 和 Azure 市场配置潜在顾客
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7fbe27845164eb7711d3531173fb440c5a3570f4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849123"
---
# <a name="configure-lead-management-for-salesforce"></a>为 Salesforce 配置潜在顾客管理

本文介绍如何设置 Salesforce 系统，以便处理 Microsoft AppSource 和 Azure 市场中产品/服务的潜在销售顾客。

> [!NOTE]
> Azure 市场不支持预填充的列表，如“国家/地区”字段的值列表。 请确保在继续操作之前未设置任何列表。 或者，可以配置 [HTTPS 终结点](./commercial-marketplace-lead-management-instructions-https.md)或 [Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)以接收潜在顾客。

## <a name="set-up-your-salesforce-system"></a>设置 Salesforce 系统

1. 登录到 Salesforce。
1. 导航到“Web-to-Lead”设置。 
    
    如果使用 Salesforce Lighting Experience
    1. 选择 Salesforce 主页上的安装程序。

       ![Salesforce 设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 在“安装程序”页，转到“平台工具” > “功能设置” > “营销” > “Web-to-Lead”    。

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    如果使用 Salesforce 经典体验：

    1. 选择 Salesforce 主页上的安装程序。

       ![Salesforce 经典设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 在“安装程序”页，选择“构建” > “自定义” > “潜在顾客” > “Web-to-Lead”    。

        ![Salesforce 经典 Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   对于这两种 Salesforce 体验，其余步骤是相同的。

1. 在“Web-to-Lead 安装程序”页上，选择“创建 Web-to-Lead 窗体”按钮 。
1. 在“Web-to-Lead 设置”中，选择“创建 Web-to-Lead 窗体” 。

    ![Salesforce Web-to-Lead 安装程序](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. 确保在“创建 Web-to-Lead 窗体”上清除 `Include reCAPTCHA in HTML` 设置并选择“生成” 。

    ![Salesforce“创建 Web-to-Lead 窗体”窗格](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. 你会看到一些 HTML 文本。 搜索文本“oid”并从 HTML 文本（仅引号之间的文本）复制“oid”值并保存。 将此值粘贴到发布门户上的“组织标识符”字段中。

    ![Salesforce“创建 Web-to-Lead 窗体”，显示 HTML“oid”值](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. 选择“完成”。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>配置你的产品/服务以将潜在顾客发送到 Salesforce

准备好在发布门户中为产品/服务配置潜在顾客管理信息后，执行以下步骤。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 选择你的产品/服务，然后转到“产品/服务设置”选项卡。

1. 在“潜在顾客”部分下，选择“连接” 。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="潜在顾客":::

1. 在“连接详细信息”弹出窗口中，选择“潜在顾客目标”的“Salesforce”，并将创建的 Web-to-Lead 窗体中的 `oid` 值粘贴到“组织标识符”字段  。

    ![“连接详细信息”弹出窗口“验证联系人电子邮件”框](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. 在“联系人电子邮件”下，为公司中应在收到新潜在顾客时收到电子邮件通知的人员输入电子邮件地址。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”。

若要确保已成功连接到潜在顾客目标，请选择“验证”。 如果成功，则在潜在顾客目标中会出现测试潜在顾客。

>[!NOTE]
>必须先完成产品/服务的其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。
