---
title: HTTPS 端点 |Azure 应用商店
description: 为 HTTPS 终结点配置潜在顾客管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262487"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 终结点配置潜在顾客管理

>[!Note]
>这些说明中使用的电源自动连接器需要付费订阅电源自动化。 请按照本文档中的说明说明说明说明。

如果合作伙伴中心中没有明确支持客户关系管理 （CRM） 系统以接收 Azure 应用商店和 AppSource 潜在顾客，则可以使用 Power 自动中的 HTTPS 终结点来处理这些潜在顾客。 使用 HTTPS 终结点，这些潜在顾客可以作为电子邮件通知发送，也可以写入 Power 自动支持的客户关系管理 （CRM） 系统。 本文中的说明将引导您完成使用 Power 自动功能创建新流的基本过程，这将生成将输入到潜在顾客管理> **HTTPS 终结点 URL**字段的发布门户中的 HTTP POST URL。 此外，还包括有关如何使用名为[Postman](https://www.getpostman.com/downloads/)的工具测试您的流程的说明，该工具可以在线找到。

## <a name="create-a-flow-using-power-automate"></a>使用电源自动功能创建流

1. 打开[流](https://flow.microsoft.com/)网页。 选择 **"登录**"，或者如果您还没有帐户，请选择 **"免费注册**"以创建一个免费的流帐户。

2. 登录并在菜单栏上选择“我的流”****。

3. 从**空白中选择 +自动 -**。

    ![我的流 = 自动 - 从空白](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. 在 *"生成自动流*"窗口中，选择 **"跳过**"。 

    ![构建自动流 - 跳过](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. 在“搜索连接器和触发器”字段中，键入“请求”以查找“请求”连接器。****
6. 在“触发器”下，选择“收到 HTTP 请求时”。****** 

    ![请求连接器 - 触发器](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. 在"*何时收到 HTTP 请求"* 窗口副本并将下面的 JSON 架构粘贴到 **"请求正文 JSON 架构**"文本框中。 Microsoft 使用此架构来包含潜在顾客数据。

    ![请求连接器 - 触发器](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>此时，您可以选择连接到 CRM 系统或配置电子邮件通知。 根据您的选择，按照其余说明进行操作。

### <a name="to-connect-to-a-crm-system"></a>连接到 CRM 系统

1. 选择 **= 新步骤**。
2. 选择您选择的 CRM 系统，通过搜索它说*搜索连接器和操作*的位置，并在 *"操作"* 部分下选择它，并创建新记录。 以下屏幕截图显示**Dynamics 365 - 创建新**记录作为示例。

    ![新建记录](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. 提供与 CRM 系统关联的**组织名称**。 从“实体名称”下拉列表中选择“潜在顾客”。********

    ![选择“潜在顾客”](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow 将显示一个用于提供潜在顾客信息的窗体。 您可以通过选择添加动态内容来映射输入请求中的项。 以下屏幕截图显示了 OfferTitle 作为示例****。

    ![添加动态内容](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 映射所需的字段，然后选择“保存”**** 以保存你的流。 创建 HTTP POST URL，可在 *"何时收到 HTTP 请求"* 窗口中访问。 使用位于 HTTP POST URL 右侧的复制控件复制此 URL - 这很重要，这样您就不会错误地错过整个 URL 的任何部分。 在发布门户中配置潜在顾客管理时，根据需要保存此 URL。

    ![收到 HTTP 请求时。](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>设置电子邮件通知

1. 现在您已完成 JSON 架构，请选择 **+ 新步骤**。
2. 在“选择操作”下，选择“操作”。********
3. 在 **"操作"** 下，选择**发送电子邮件（Office 365 Outlook）。**

    >[!Note]
    >如果要使用其他电子邮件提供商搜索，请选择 *"发送电子邮件通知（邮件）"* 作为操作。

    ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. 在 **"发送电子邮件窗口"中**，配置以下必填字段：

   - **至**- 输入至少一个有效的电子邮件地址，其中将发送潜在顾客。
   - **主题** - Flow 将提供用于添加动态内容的选项，例如以下屏幕截图中的 **LeadSource**。 首先键入字段名称，然后从弹出窗口单击动态内容选取列表。 

        >[!Note] 
        > 添加字段名称时，可以使用"："每个字段名称进行跟踪，然后输入以创建新行。 添加字段名称后，可以从动态选取列表中添加每个关联的参数。

        ![使用动态内容添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **正文**- 从动态内容选取列表中，在电子邮件正文中添加所需的信息。 例如，姓氏、名字、电子邮件和公司。 <br> <br> 完成设置电子邮件通知后，该通知类似于以下屏幕截图中的示例。


       ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. 选择“保存”以完成 Flow 设置。**** HTTP POST URL 将在 *"何时收到 HTTP 请求"* 窗口中创建并可访问。 使用位于 HTTP POST URL 右侧的复制控件复制此 URL - 这很重要，这样您就不会错误地错过整个 URL 的任何部分。 在发布门户中配置潜在顾客管理时，根据需要保存此 URL。

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>正在测试

您可以使用名为[Postman](https://app.getpostman.com/app/download/win64)的工具（可以在线下载）测试一切工作正常。 这适用于 Windows。 

1. 启动 Postman 并选择 **"新** > **请求"** 以设置测试工具。 

   ![请求设置测试工具](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. 填写 *"保存请求"* 窗体，然后**保存到**您创建的文件夹。

   ![保存请求](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. 从下拉列表中选择 **"正在关闭"。** 

   ![测试我的流](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. 从您在电源自动功能中创建的流中粘贴 HTTP POST URL，其中显示*输入请求 URL*。

   ![粘贴 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. 返回[Flow](https://flow.microsoft.com/)并找到您创建的发送潜在顾客的流，通过从"流"菜单栏转到 **"我的流**"。  选择流名称旁边的 3 个点，然后选择 **"编辑**"。

   ![我的流 - 编辑](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 选择右上角的 **"测试**"，选择"我将执行触发器操作"，然后选择 **"测试**"。 您将在屏幕顶部看到一个指示，指示测试已启动

   ![测试流 - 触发器](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. 返回您的 Postman 应用，然后选择"在粘贴 HTTPS URL 的位置旁边**发送**"。

   ![测试我的流 - 发送](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. 返回您的流并检查结果。 如果一切按预期工作，您将看到一条消息，指示它成功。

   ![流 - 检查结果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 您还应收到一封电子邮件。 检查您的电子邮件收件箱。 

    >[!Note] 
    >如果您没有看到来自测试的电子邮件，请检查您的垃圾邮件和垃圾邮件文件夹。 下面您将只注意到在配置电子邮件通知时添加的字段标签。 如果这是从您的报价生成的实际潜在顾客，您还会在正文和主题行中看到来自"潜在顾客联系人"的实际信息。

   ![收到的电子邮件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作：

1. 导航到产品 **/服务"产品/服务"设置**页面。
2. 在"潜在顾客管理"部分下选择 **"连接**"。
3. 在"连接详细信息"弹出窗口上，为**潜在顾客目标**选择**HTTPS 终结点**，然后从通过按照前面的步骤创建的流粘贴到**HTTPS 终结点 URL**字段中的 HTTP POST URL 中。
4. **联系电子邮件**- 为公司中应接收新潜在顾客的电子邮件通知的人员提供电子邮件。 您可以通过用分号分隔它们来提供多封电子邮件。
5. 选择“确定”  。

要确保已成功连接到潜在顾客目标，请单击验证按钮。 如果成功，您将在潜在顾客目标中具有测试潜在顾客。

>[!Note] 
>您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。

生成潜在顾客后，Microsoft 会将潜在顾客发送到 Flow，然后这些潜在顾客将路由到配置的 CRM 系统或电子邮件地址。

![引线管理 - 连接](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![连接详细信息](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![连接详细信息](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

