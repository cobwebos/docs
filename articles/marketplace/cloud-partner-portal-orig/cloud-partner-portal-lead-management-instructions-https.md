---
title: 使用 HTTPS 终结点配置潜在客户管理 |Azure Marketplace
description: 了解如何使用 HTTP 终结点处理 Microsoft AppSource 和 Azure Marketplace 潜在顾客。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770194"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 终结点配置潜在顾客管理

可以使用 HTTPS 终结点处理 Microsoft AppSource 和 Azure Marketplace 潜在顾客。 这些潜在顾客可以写入客户关系管理（CRM）系统，也可以作为电子邮件通知发送。 本文介绍如何使用[Microsoft 自动](https://powerapps.microsoft.com/automate-processes/)自动化服务来配置潜在客户管理。

## <a name="create-a-flow-using-microsoft-power-automate"></a>使用 Microsoft 电源自动化创建流

1. 打开 "[自动启动](https://flow.microsoft.com/)" 网页。 选择“登录”**** 或者选择“免费注册”**** 来创建免费的流帐户。

1. 登录并在菜单栏上选择“我的流”****。
    > [!div class="mx-imgBorder"]
    > ![我的流](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. 在 " **+ 新建**" 下，选择 " **+ 即时"-从空白**。
    > [!div class="mx-imgBorder"]
    > ![从空白创建](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 命名你的流，然后在 "**选择触发此流的方式**" 下，选择**接收 HTTP 请求的时间**。

    > [!div class="mx-imgBorder"]
    > ![选择“收到 HTTP 请求时”触发器](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 单击 flow 步骤以将其展开。

    > [!div class="mx-imgBorder"]
    > ![展开 flow 步骤](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. 使用以下方法之一来配置**请求正文 JSON 架构**：

   - 将本文末尾的 [JSON 架构](#json-schema)复制到“请求正文 JSON 架构”文本框中。****
   - 选择“使用示例有效负载生成架构”。**** 在“输入或粘贴示例 JSON 有效负载”文本框中，粘贴该 [JSON 示例](#json-example)。**** 选择“完成”以创建架构。****

   >[!Note]
   >此时，在 Flow 中，可以连接到 CRM 系统或者配置电子邮件通知。

### <a name="to-connect-to-a-crm-system"></a>连接到 CRM 系统

1. 选择 " **+ 新步骤**"。
2. 选择所选的 CRM 系统以及用于新建记录的操作。 以下屏幕截图显示了“Dynamics 365 - 新建记录”**** 作为示例。

    ![新建记录](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. 提供**组织名称**，即，连接器的连接输入。 从“实体名称”下拉列表中选择“潜在顾客”。********

    ![选择“潜在顾客”](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow 将显示一个用于提供潜在顾客信息的窗体。 可以通过选择添加动态内容来映射输入请求中的项。 以下屏幕截图显示了 OfferTitle 作为示例****。

    ![添加动态内容](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. 映射所需的字段，然后选择“保存”**** 以保存你的流。

6. 将在请求中创建一个 HTTP POST URL。 复制此 URL 并使用它作为 HTTPS 终结点。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>设置电子邮件通知

1. 选择 " **+ 新步骤**"。
2. 在“选择操作”下，选择“操作”。********
3. 在“操作”下面，选择“发送电子邮件”。********

    ![添加电子邮件操作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. 在“发送电子邮件”中配置以下必填字段：****

   - **发件人** - 至少输入一个有效的电子邮件地址。
   - **主题** - Flow 将提供用于添加动态内容的选项，例如以下屏幕截图中的 **LeadSource**。

     ![使用动态内容添加电子邮件操作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **正文** - 在“动态内容”列表中，添加要包含在电子邮件正文中的信息。 例如，姓氏、名字、电子邮件和公司。

   完成设置电子邮件通知后，该通知类似于以下屏幕截图中的示例。

   ![添加电子邮件操作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. 选择“保存”以完成 Flow 设置。****

6. 将在请求中创建一个 HTTP POST URL。 复制此 URL 并使用它作为 HTTPS 终结点。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

当你为产品/服务配置潜在客户管理信息时，请选择 "**客户" 目标**的**HTTPS 终结点**，并将其粘贴到你在上一步骤中复制的 HTTP POST URL。  

![添加动态内容](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

当生成潜在顾客时，Microsoft 会将潜在客户发送到您的电源自动流，这会路由到您配置的 CRM 系统或电子邮件地址。

## <a name="json-schema-and-example"></a>JSON 架构和示例

JSON 测试示例使用以下架构：

### <a name="json-schema"></a>JSON 架构

``` json
{
  "$schema": "https://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "Description": {
      "id": "/properties/Description",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```

可以复制和编辑下面的 JSON 示例，以用作流中的测试。

### <a name="json-example"></a>JSON 示例

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请在云合作伙伴门户中配置[潜在顾客](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)。
