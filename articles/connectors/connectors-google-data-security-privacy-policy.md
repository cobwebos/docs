---
title: Google 连接器的数据安全和隐私策略
description: 了解 Google 安全和隐私策略对 Google 连接器（如 Gmail）在 Azure 逻辑应用中的影响
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 590ad6a52d768c7e59d8d97691e146205e43cadd
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628702"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure 逻辑应用中 Google 连接器的数据安全和隐私策略

从**2020**开始，，由于 Google 的[数据安全和隐私策略](https://www.blog.google/technology/safety-security/project-strobe/)的更改，可能会影响使用[Gmail 连接器](https://docs.microsoft.com/connectors/gmail/)的逻辑应用工作流。 如果逻辑应用使用 gmail 连接器，其中包含 Gmail 使用者帐户（以@gmail.com或@googlemail.com结尾的电子邮件地址），则逻辑应用只能使用特定[的 Google 批准触发器、操作和连接器](#approved-connectors)。 

> [!NOTE]
> 如果逻辑应用将 Gmail 连接器与 G Suite 企业帐户（带有自定义域的电子邮件地址）结合使用，则逻辑应用不会受到影响，也不会限制使用 Gmail 连接器。

## <a name="affected-logic-apps"></a>受影响的逻辑应用

如果有使用 Gmail 连接器的逻辑应用，则会收到有关潜在影响的逻辑应用的电子邮件。 但从**2020 年6月15日**起，将禁用任何不符合的工作流。 可以执行以下任一操作：

* 按照[本主题中的步骤](#update-affected-workflows)更新受影响的逻辑应用。 需要创建 Google 客户端应用，它提供了用于在 Gmail 触发器或操作中进行身份验证的客户端 ID 和客户端密码。

* 更新受影响的逻辑应用，以便在重新启用已禁用的逻辑应用之前仅使用[Google 批准的连接器](#approved-connectors)。

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google 批准的连接器

在此策略下，使用 Gmail 使用者帐户时，可以使用 Gmail 连接器，其中仅包含特定的 Google 批准服务，这些服务可能会发生更改。 我们的工程团队继续使用 Google 向此列表添加更多服务。 现在，在使用 Gmail 使用者帐户时，可以在与 Gmail 连接器相同的逻辑应用工作流中使用 Google 批准的触发器、操作和连接器：

* 逻辑应用内置触发器和操作：批处理、控制、数据操作、日期时间、平面文件、液体、请求、计划、变量和 XML

* Google 服务： Gmail、Google Calendar、Google Contacts、Google Drive、Google Sheets 和 Google Task

* 批准的 Microsoft 服务： Dynamics 365、Excel Online、Microsoft 团队、Office 365、OneDrive 和 SharePoint Online

* 用于客户管理的数据源的连接器： FTP、RSS、SFTP、SMTP 和 SQL Server

有关最新信息，请参阅[Gmail 连接器技术参考文档](https://docs.microsoft.com/connectors/gmail/)。

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>受影响的逻辑应用的步骤

如果必须在逻辑应用中将 Gmail 连接器与 Gmail 使用者帐户和 Google 非批准连接器一起使用，则可以创建自己的 Google 应用，以便在企业中使用个人或内部。 对于此方案，需要执行以下高级步骤：

1. 使用[GOOGLE API 控制台](https://console.developers.google.com)创建 google 客户端应用。

1. 在 Gmail 连接器中，使用 Google 客户端应用的客户端 ID 和客户端机密值。

有关详细信息，请参阅[Gmail 连接器的技术参考文档](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。

### <a name="create-google-client-app"></a>创建 Google 客户端应用

若要为客户端应用程序设置项目，请使用[GOOGLE API 控制台向导](https://console.developers.google.com/start/api?id=gmail&credential=client_key)并按照说明进行操作。 或者，有关详细步骤，请查看[Gmail 连接器技术参考文档](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)中的说明。

完成后，屏幕看起来像此示例，只不过你将拥有自己的**客户端 ID**和**客户端密钥**值，你稍后会在逻辑应用中使用它。

![Google 客户端应用的客户端 ID 和客户端密码](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>在逻辑应用中使用客户端应用设置

若要在 Gmail 触发器或操作中使用 Google 客户端应用的客户端 ID 和客户端密钥，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果要添加新的 Gmail 触发器或操作并创建全新的连接，请继续执行下一步。 否则，在 "Gmail 触发器" 或 "操作" 中，选择 "**更改连接** > **添加新**"，例如：

   ![选择 "更改连接" > "添加新"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. 提供连接信息，例如：

   ![提供连接信息](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **身份验证类型** | **自带应用程序** | 指定将使用自己的客户端应用进行身份验证。 |
   | **客户端 ID** | <*client-ID*> | Google 客户端应用的客户端 ID |
   | **客户端密码** | <*client-secret*> | Google 客户端应用的客户端密码 |
   ||||

1. 完成后，选择 "**登录**"。

   此时会出现一个页面，其中显示你创建的客户端应用。 如果你使用的是 Gmail 使用者帐户，则可能会出现一个页面，其中显示你的客户端应用未通过 Google 验证，并提示你首先允许访问你的 Google 帐户。

   ![提示访问 Google 帐户](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. 如有必要，选择 "**允许**"。

   你现在可以在逻辑应用中使用 Gmail 连接器，而不会受到任何限制。

## <a name="next-steps"></a>后续步骤

详细了解[Gmail 连接器](https://docs.microsoft.com/connectors/gmail/)
