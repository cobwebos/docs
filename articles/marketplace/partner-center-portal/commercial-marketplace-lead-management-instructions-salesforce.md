---
title: 配置 Salesforce 的潜在客户管理 |Azure Marketplace
description: 为 Azure Marketplace 客户配置 Salesforce 的潜在客户管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812156"
---
# <a name="configure-lead-management-for-salesforce"></a>为 Salesforce 配置潜在顾客管理

本文介绍如何将 Salesforce 系统设置为处理 marketplace 产品/服务的销售潜在客户。

## <a name="set-up-your-salesforce-system"></a>设置 Salesforce 系统

1. 登录到 Salesforce。
2. 如果使用的是 Salesforce 照明体验。
    1. 从 "Salesforce 主页" 中选择 "**设置**"。
    ![Salesforce 设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 从 "设置" 页中，通过左侧导航导航到 "**平台工具-> 功能设置-> 市场营销->" "Web 到潜在客户**"。
    ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. 如果使用的是 Salesforce 经典体验：
    1. 从 "Salesforce 主页" 中选择 "**设置**"。
    ![Salesforce 经典设置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 在 "设置" 页中，通过左侧导航导航到 **> 自定义 > 潜在客户-> "Web 到潜在客户**"。
    ![Salesforce 经典 web 到潜在](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

无论你使用哪种 Salesforce 体验，其他说明都是相同的。

4. 在 " **web 到客户设置" 页**上，选择 "**创建 Web 到主导窗体**" 按钮。
5. 在“Web-to-Lead 设置”中，选择“创建 Web-to-Lead 窗体”。
    ![Salesforce-Web 到主导安装](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 在 "**创建 Web 到潜在顾客" 窗体**上，确保未选中 "`the Include reCAPTCHA in HTML`" 设置，然后选择 "**生成**"。 
    ![Salesforce-创建一个 Web 到主导窗体](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 你将看到一些 HTML 文本。 搜索文本 "oid" 并从 HTML 文本（仅限引号之间的文本）中复制**oid 值**并保存它。 将此值粘贴到发布门户上的 "**组织标识符**" 字段。
    ![Salesforce-创建一个 Web 到主导窗体](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **已完成**。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>配置你的产品/服务以将潜在客户发送到 Salesforce

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤：

1. 导航到产品/服务的 "**产品/服务设置**" 页。
1. 选择 "潜在顾客管理" 部分下的 "**连接**"。
    ![潜在客户管理-连接](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在 "连接详细信息" 弹出窗口中，选择 " **Salesforce** " 作为 "**潜在顾客目标**"，然后在 "**组织标识符**" 字段中按前面的步骤创建的 web 到主导窗体中粘贴 `oid`。

1. 选择“保存”。 

    >[!Note]
    >您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。

    ![连接详细信息-选择潜在客户目标](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![连接详细信息-选择潜在客户目标](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)