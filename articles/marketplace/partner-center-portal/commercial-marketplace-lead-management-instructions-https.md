---
title: HTTPS 终结点 |Azure Marketplace
description: 为 HTTPS 终结点配置潜在顾客管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902190"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 终结点配置潜在顾客管理

如果你的客户关系管理（CRM）系统未在合作伙伴中心获得 Azure Marketplace 和 AppSource 潜在顾客的明确支持，你可以在 MS Flow 中使用 HTTPS 终结点来处理这些潜在顾客。 使用 HTTPS 终结点时，这些潜在顾客可以作为电子邮件通知发送，也可以写入 MS Flow 支持的客户关系管理（CRM）系统中。 本文中的说明将指导你完成使用 Microsoft Flow 创建新流的基本过程，该过程将生成 HTTP POST URL，你将在 "潜在顾客管理 > **HTTPS 终结点 URL** " 字段中输入该 url。 此外，还提供了有关如何使用名为[Postman](https://www.getpostman.com/downloads/)的工具（可在线找到）来测试流的说明。

## <a name="create-a-flow-using-microsoft-flow"></a>使用 Microsoft Flow 创建流

1. 打开[流](https://flow.microsoft.com/)网页。 选择 "**登录**"，如果还没有帐户，请选择 "**免费注册**"，创建一个免费的流帐户。

2. 登录并在菜单栏上选择“我的流”。

3. 选择 " **+ 自动–从空白**"。

    ![我的流 + 自动-从空白](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. 在 "*生成自动流*" 窗口中，选择 "**跳过**"。 

    ![生成自动流-跳过](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. 在“搜索连接器和触发器”字段中，键入“请求”以查找“请求”连接器。
6. 在“触发器”下，选择“收到 HTTP 请求时”。 

    ![请求连接器-触发器](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. 在 "*收到 HTTP 请求时*" 窗口中，将下面的 JSON 架构复制并粘贴到 "**请求正文 json 架构**" 文本框中。 Microsoft 使用此架构来包含您的潜在客户数据。

    ![请求连接器-触发器](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON 架构**

    ```JSON
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

>[!Note]
>此时，你可以选择连接到 CRM 系统或配置电子邮件通知。 根据你的选择，按照其余说明进行操作。

### <a name="to-connect-to-a-crm-system"></a>连接到 CRM 系统

1. 选择“+新建步骤”。
2. 通过在显示 "*搜索连接器和操作*" 的位置进行搜索来选择所选的 CRM 系统，然后在 "*操作*" 部分下选择它，并在 "操作" 部分下选择该系统以创建新记录。 以下屏幕截图显示**Dynamics 365-创建**新记录作为示例。

    ![创建新记录](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. 提供与 CRM 系统关联的**组织名称**。 从“实体名称”下拉列表中选择“潜在顾客”。

    ![选择“潜在顾客”](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow 将显示一个用于提供潜在顾客信息的窗体。 可以通过选择添加动态内容来映射输入请求中的项。 以下屏幕截图显示了 OfferTitle 作为示例。

    ![添加动态内容](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 映射所需的字段，然后选择“保存”以保存你的流。 将创建一个 HTTP POST URL，在*收到 http 请求时*可在中访问。 使用位于 HTTP POST URL 右侧的复制控件复制此 URL-这一点非常重要，这样您就不会误错过整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置 "潜在客户管理" 时将需要它。

    ![收到 HTTP 请求时。](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>设置电子邮件通知

1. 完成 JSON 架构后，选择 " **+ 新建步骤**"。
2. 在“选择操作”下，选择“操作”。
3. 在 "**操作**" 下，选择 "**发送电子邮件（Office 365 Outlook）**"。

    >[!Note]
    >如果要使用不同的电子邮件提供程序搜索，并选择 "*发送电子邮件通知（邮件）* " 作为操作。

    ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. 在 "**发送电子邮件**" 窗口中，配置以下必填字段：

   - 输入至少一个**有效的电子**邮件地址，将在其中发送潜在顾客。
   - **主题** - Flow 将提供用于添加动态内容的选项，例如以下屏幕截图中的 **LeadSource**。 首先键入字段名称，然后在弹出窗口中单击 "动态内容选取列表"。 

        >[!Note] 
        > 添加字段名称时，可以在每个字段中使用 "："，然后按 Enter 创建新行。 添加字段名称后，可以从动态选取列表中添加每个关联的参数。

        ![使用动态内容添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **正文**-从动态内容选取列表中，在电子邮件正文中添加所需的信息。 例如，姓氏、名字、电子邮件和公司。 <br> <br> 完成设置电子邮件通知后，该通知类似于以下屏幕截图中的示例。


       ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. 选择“保存”以完成 Flow 设置。 在*收到 http 请求时*，会创建一个 HTTP POST URL，可在中访问该 URL。 使用位于 HTTP POST URL 右侧的复制控件复制此 URL-这一点非常重要，这样您就不会误错过整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置 "潜在客户管理" 时将需要它。

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>正在测试

你可以使用以下步骤来测试一切是否按预期方式工作：使用名为[Postman](https://app.getpostman.com/app/download/win64)的工具，该工具可以在线下载。 这适用于 Windows。 

1. 启动 Postman 并选择 "**新** > **请求**" 以设置测试工具。 

   ![设置测试工具的请求](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. 填写*保存请求*窗体，然后**将其保存**到所创建的文件夹中。

   ![保存请求](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. 从下拉列表中选择 " **POST** "。 

   ![测试我的流](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. 从 "MS Flow" 中创建的流粘贴 HTTP POST URL，其中显示的是 "*输入请求 URL*"。

   ![粘贴 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. 返回到 "流" [，并通过](https://flow.microsoft.com/)转到 "流" 菜单栏中的 "**我的流**"，找到你创建的用于发送线索的流程。  选择流名称旁边的3个点，然后选择 "**编辑**"。

   ![我的流-编辑](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 在右上角选择 "**测试**"，选择 "我要执行触发器操作"，然后选择 "**测试**"。 屏幕顶部将显示指示测试已启动的指示

   ![测试流-触发器](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. 返回到 Postman 应用，并在粘贴 HTTPS URL 的位置选择 "**发送**"。

   ![测试我的流-发送](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. 返回到流并检查结果。 如果一切按预期运行，将看到一条消息，指示它已成功完成。

   ![流检查结果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 你还应收到一封电子邮件。 检查电子邮件收件箱。 

    >[!Note] 
    >如果看不到测试中的电子邮件，请检查垃圾邮件和垃圾邮件文件夹。 下面你会注意到在配置电子邮件通知时添加的字段标签。 如果这是你的产品/服务生成的实际潜在顾客，你还会在正文和主题行中看到潜在顾客联系人的实际信息。

   ![已收到电子邮件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤：

1. 导航到产品/服务的 "**产品/服务设置**" 页。
2. 选择 "潜在顾客管理" 部分下的 "**连接**"。
3. 在 "连接详细信息" 弹出窗口中，选择 "客户端**目标**的**HTTPS 终结点**"，并将通过以下步骤创建的流中的 HTTP POST Url 粘贴到 " **HTTPS 终结点 URL** " 字段。
4. 选择**保存**。 

>[!Note] 
>您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。

生成潜在顾客后，Microsoft 会将潜在顾客发送到 Flow，然后这些潜在顾客将路由到配置的 CRM 系统或电子邮件地址。

![潜在客户管理-连接](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![连接详细信息](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![连接详细信息](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

