---
title: 使用 HTTPS 终结点进行客户端管理-Microsoft 商用 marketplace
description: 了解如何使用 Power 自动功能和 HTTPS 终结点来管理 Microsoft AppSource 和 Azure Marketplace 中的潜在客户。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7a4fc57b3be8dd59997ef2bfc9624892cf726160
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790977"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>使用 HTTPS 终结点管理商业市场主管

如果你的客户关系管理（CRM）系统未在合作伙伴中心获得支持 Microsoft AppSource 和 Azure Marketplace 主管，你可以在 "[自动](https://powerapps.microsoft.com/automate-processes/)" 中使用 HTTPS 终结点来处理这些潜在顾客。 使用 HTTPS 终结点时，可以通过电子邮件通知形式发送商业市场主管，也可以将其写入到电源自动支持的 CRM 系统。

本文介绍如何在 "自动启动" 中创建新流，以生成将用于在合作伙伴中心配置潜在顾客的 HTTP POST URL。 其中还包括用[Postman](https://www.getpostman.com/downloads/)测试流的步骤。

>[!NOTE]
>这些说明中使用的电源自动连接器需要付费订阅才能实现自动化。 在配置此流之前，请务必考虑此情况。

## <a name="create-a-flow-by-using-power-automate"></a>使用电源自动执行创建流

1. 打开 "[自动启动](https://flow.microsoft.com/)" 网页。 选择“登录”  。 如果还没有帐户，请选择 "**免费注册**"，创建一个免费的 "自动完成电源" 帐户。

1. 登录，然后在菜单中选择 **"我的流**"。

    ![登录我的流](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. 在 " **+ 新建**" 下，选择 " **+ 即时"-从空白**。

    ![我的流 + 自动化-从空白](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 命名你的流，然后在 "**选择触发此流的方式**" 下，选择**接收 HTTP 请求的时间**。

    ![生成自动流窗口 "跳过" 按钮](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 单击 flow 步骤以将其展开。

    ![展开 flow 步骤](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. 使用以下方法之一来配置**请求正文 JSON 架构**：

    - 将 JSON 架构复制到 "**请求正文 JSON 架构**" 文本框中。
    - 选择“使用示例有效负载生成架构”。**** 在 "**输入或粘贴示例 JSON 负载**" 文本框中，粘贴 JSON 示例。 选择“完成”以创建架构。****

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

    **JSON 示例**
    
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

>[!NOTE]
>此时，你可以选择连接到 CRM 系统或配置电子邮件通知。 根据你的选择，按照其余说明进行操作。

### <a name="connect-to-a-crm-system"></a>连接到 CRM 系统

1. 选择 " **+ 新步骤**"。
1. 通过在其中显示 "**搜索连接器和操作**" 的位置，选择所选的 CRM 系统。 在 "**操作**" 选项卡上选择该选项，并将 "操作" 选项卡用于创建新记录。 下面的屏幕显示了 "**创建新记录" （Dynamics 365）** 作为示例。

    ![新建记录](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. 提供与 CRM 系统关联的**组织名称**。 从 "**实体名称**" 下拉列表中选择 "**潜在客户**"。

    ![选择“潜在顾客”](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power 自动功能显示了用于提供潜在顾客信息的窗体。 可以通过选择添加动态内容来映射输入请求中的项。 下面的屏幕显示**OfferTitle**作为示例。

    ![添加动态内容](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. 映射所需的字段，然后选择 "**保存**" 以保存流。 在**收到 http 请求时**，会创建一个 HTTP POST URL，可在中访问该 URL。 使用复制控件复制此 URL，该控件位于 HTTP POST URL 的右侧。 使用复制控件非常重要，这样就不会错过整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置潜在客户管理时需要用到它。

    ![收到 HTTP 请求时](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>设置电子邮件通知

1. 完成 JSON 架构后，选择 " **+ 新建步骤**"。
1. 在“选择操作”下，选择“操作”。********
1. 在 "**操作**" 选项卡上，选择 "**发送电子邮件（Office 365 Outlook）**"。

    >[!NOTE]
    >如果要使用其他电子邮件提供程序，请搜索并选择 "**发送电子邮件通知（邮件）** " 作为操作。

    ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. 在 "**发送电子邮件**" 窗口中，配置以下必填字段：

   - **目标**：输入至少一个有效的电子邮件地址，将在其中发送潜在顾客。
   - **主题**： Power 自动功能提供了添加动态内容的选项，如以下屏幕中所示的**LeadSource** 。 首先输入字段名称。 然后从弹出窗口中选择 "动态内容选取列表"。 

        >[!NOTE] 
        > 添加字段名称时，可以在每个名称后面加上一个冒号（:)然后选择**Enter**创建新行。 添加字段名称后，可以添加动态选取列表中的每个关联参数。

        ![使用动态内容添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **正文**：从动态内容选取列表中，在电子邮件正文中添加所需的信息。 例如，使用 LastName、FirstName、Email 和 Company。 设置完电子邮件通知后，其外观如以下屏幕中所示。


       ![电子邮件通知示例](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. 选择“保存”以完成 Flow 设置。**** 在**收到 http 请求时**，会创建一个 HTTP POST URL，可在中访问该 URL。 使用复制控件复制此 URL，该控件位于 HTTP POST URL 的右侧。 使用此控件非常重要，这样就不会遗漏整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置潜在客户管理时需要用到它。

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>测试

可以通过[Postman](https://app.getpostman.com/app/download/win64)测试配置。 Windows 提供了 Postman 的在线下载。 

1. 启动 Postman，然后选择 "**新建** > **请求**" 以设置测试工具。 

   ![请求设置你的测试工具](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. 填写**保存请求**窗体，然后将其保存到所创建的文件夹中。

   ![保存请求窗体](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. 从下拉列表中选择 " **POST** "。 

   ![测试我的流](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. 将 HTTP POST URL 从在 "电源自动" 中创建的流粘贴到 "**输入请求 URL**"。

   ![粘贴 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. 返回 "[电源自动](https://flow.microsoft.com/)"。 从 "电源自动菜单" 菜单栏中转到 "**我的流程**"，找到你创建的用于发送线索的流程。 选择流名称旁边的省略号以查看更多选项，然后选择 "**编辑**"。


1. 在右上角选择 "**测试**"，然后选择 "**执行触发器操作**"，然后选择 "**测试**"。 你会在屏幕上看到测试已开始的指示。

   ![我要执行触发器操作选项](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. 返回到 Postman 应用，并选择 "**发送**"。

   ![发送按钮](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. 返回到流并检查结果。 如果一切按预期运行，你会看到一条消息，指示流已成功。

   ![检查结果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 你还应收到一封电子邮件。 检查电子邮件收件箱。 

    >[!NOTE] 
    >如果看不到测试中的电子邮件，请检查垃圾邮件和垃圾邮件文件夹。 在下面的屏幕中，你会注意到在配置电子邮件通知时添加的字段标签。 如果这是你的产品/服务生成的实际潜在顾客，你还会在正文和主题行中看到潜在顾客联系人的实际信息。

   ![已收到电子邮件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 选择产品/服务，并中转到 "**产品/服务" 设置**选项卡。

1. 在 "**潜在客户管理**" 部分下，选择 "**连接**"。 
    ![潜在客户管理连接按钮](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

1. 在 "**连接详细信息**" 弹出窗口中，选择 "**潜在顾客目标**的**HTTPS 终结点**"。 将前面步骤中创建的流中的 HTTP POST URL 粘贴到 " **HTTPS 终结点 URL** " 字段。
    ![连接详细信息联系电子邮件](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. 在 "**联系人电子邮件**" 下，输入公司中应在收到新潜在客户时接收电子邮件通知的人员的电子邮件地址。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”  。

若要确保已成功连接到潜在客户目标，请选择 "**验证**" 按钮。 如果成功，则在潜在客户目标中会出现测试主管。

>[!NOTE] 
>您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。

当生成潜在顾客时，Microsoft 会将潜在顾客发送到流中。 潜在顾客路由到您配置的 CRM 系统或电子邮件地址。
