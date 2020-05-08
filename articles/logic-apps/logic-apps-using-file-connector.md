---
title: 连接到本地文件系统
description: 通过 Azure 逻辑应用中的本地数据网关，使用文件系统连接器将连接到本地文件系统的任务和工作流自动化
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 79c99a8ba2712fe69ec6d3b9b9d32ddf6aa081cb
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580630"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>使用 Azure 逻辑应用连接到本地文件系统

使用 Azure 逻辑应用和文件系统连接器，可以创建自动任务和工作流，用于创建和管理本地文件共享上的文件，例如：

- 创建、获取、追加、更新和删除文件。
- 列出文件夹或根文件夹中的文件。
- 获取文件内容和元数据。

本文介绍如何根据本示例方案所述连接到本地文件系统：将上传至 Dropbox 的文件复制到文件共享，然后发送一封电子邮件。 为了安全连接和访问本地系统，逻辑应用将使用[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)。 如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。 有关连接器特定的技术信息，请参阅[文件系统连接器参考](/connectors/filesystem/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 将逻辑应用连接到本地系统（例如文件系统服务器）之前，需要[安装并设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 这样，便可以指定在从逻辑应用创建文件系统连接时要使用网关安装。

* 一个 [Dropbox 帐户](https://www.dropbox.com/)，可以在注册后免费获取。 帐户凭据是在逻辑应用和 Dropbox 帐户之间创建连接所必需的。

* 访问特定的计算机，其中的文件系统是你需要使用的。 例如，如果在与文件系统相同的计算机上安装数据网关，则需要该计算机的帐户凭据。

* 逻辑应用支持的提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](https://docs.microsoft.com/connectors/)。 此逻辑应用使用 Office 365 Outlook 帐户。 如果使用其他电子邮件帐户，整个步骤仍然是相同的，但 UI 可能稍有不同。

  > [!IMPORTANT]
  > 如果要使用 Gmail 连接器，则只有 G Suite 业务帐户才能在逻辑应用中使用此连接器而不进行限制。 如果你有 Gmail 使用者帐户，则只能将此连接器与特定的 Google 批准服务一起使用，也可以[创建 google 客户端应用以用于通过 Gmail 连接器进行身份验证](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅[Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 对于本示例，需要一个空白逻辑应用。

## <a name="add-trigger"></a>添加触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在搜索框中，输入“dropbox”作为筛选器。 在触发器列表中选择以下触发器：“创建文件时”****

   ![选择 Dropbox 触发器](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. 使用 Dropbox 帐户凭据登录，并授权 Azure 逻辑应用访问你的 Dropbox 数据。

1. 提供触发器的所需信息。

   ![Dropbox 触发器](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>添加操作

1. 在触发器下，选择“下一步”。**** 在搜索框中，输入“文件系统”作为筛选器。 从 "操作" 列表中，选择此操作：**创建文件**

   ![查找文件系统连接器](media/logic-apps-using-file-connector/find-file-system-action.png)

1. 如果还没有到文件系统的连接，系统会提示创建连接。

   ![创建连接](media/logic-apps-using-file-connector/file-system-connection.png)

   | 属性 | 必须 | Value | 说明 |
   | -------- | -------- | ----- | ----------- |
   | **连接名称** | 是 | <*连接-名称*> | 连接使用的名称 |
   | **根文件夹** | 是 | <*根文件夹-名称*> | 文件系统的根文件夹，例如，如果安装了本地数据网关，比如安装了本地数据网关的计算机上的本地文件夹，或者计算机可以访问的网络共享文件夹。 <p>例如： `\\PublicShare\\DropboxFiles` <p>根文件夹是主要的父文件夹，用作所有与文件有关的操作的相对路径。 |
   | **身份验证类型** | 否 | <*身份验证类型*> | 文件系统使用的身份验证类型： **Windows** |
   | **用户名** | 是 | <*domain*>域\\*username*用户名<> <p>-或- <p><*本地*>\\计算机<*用户名*> | 具有文件系统文件夹的计算机的用户名。 <p>如果文件系统文件夹与本地数据网关在同一台计算机上，则可以使用 <*本地计算机*>\\<*用户名*>。 |
   | **密码** | 是 | <*你的密码*> | 拥有文件系统的计算机密码 |
   | **gateway** | 是 | <*已安装-网关-名称*> | 以前安装的网关的名称 |
   |||||

1. 完成后，选择“创建”****。

   逻辑应用会配置和测试连接，以确保连接正常运行。 如果正确建立连接，会显示用于之前所选操作的选项。

1. 在“创建文件”操作中，提供用于将文件从 Dropbox 复制到本地文件共享中的根文件夹的详细信息****。 若要添加前面步骤的输出，请在框中单击，然后在显示动态内容列表时从可用字段中进行选择。

   ![创建文件操作](media/logic-apps-using-file-connector/create-file-filled.png)

1. 现在添加一个发送电子邮件的 Outlook 操作，使相应的用户知道已创建新文件。 输入收件人、标题和电子邮件正文。 若要测试，可以使用自己的电子邮件地址。

   ![发送电子邮件操作](media/logic-apps-using-file-connector/send-email.png)

1. 保存逻辑应用。 通过将文件上传到 Dropbox 来测试应用。

   逻辑应用应会将该文件复制到本地文件共享，并向收件人发送一封有关已复制的文件的电子邮件。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，如连接器的 Swagger 文件所述的触发器、操作和限制，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/fileconnector/)。

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解如何[连接到本地数据](../logic-apps/logic-apps-gateway-connection.md) 
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
