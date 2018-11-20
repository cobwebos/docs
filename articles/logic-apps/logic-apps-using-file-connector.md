---
title: 连接到本地文件系统 - Azure 逻辑应用 | Microsoft Docs
description: 通过 Azure 逻辑应用中的本地数据网关，使用文件系统连接器将连接到本地文件系统的任务和工作流自动化
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 0c30ffec58b1542fa80cf0c9873a0e6df8641104
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232539"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>使用 Azure 逻辑应用连接到本地文件系统

使用文件系统连接器和 Azure 逻辑应用，可以创建能够在本地文件共享中创建和管理文件的自动化任务与工作流，例如：  

- 创建、获取、追加、更新和删除文件
- 列出文件夹或根文件夹中的文件。
- 获取文件内容和元数据。

本文介绍如何根据本示例方案所述连接到本地文件系统：将上传至 Dropbox 的文件复制到文件共享，然后发送一封电子邮件。 为了安全连接和访问本地系统，逻辑应用将使用[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 将逻辑应用连接到本地系统（例如文件系统服务器）之前，需要[安装并设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 这样，便可以指定在从逻辑应用创建文件系统连接时要使用网关安装。

* [Drobox 帐户](https://www.dropbox.com/)和用户凭据

  你的凭据授权逻辑应用创建连接并访问你的 Drobox 帐户。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 对于本示例，需要一个空白逻辑应用。

## <a name="add-trigger"></a>添加触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在搜索框中，输入“dropbox”作为筛选器。 在触发器列表中选择以下触发器：“创建文件时” 

   ![选择 Dropbox 触发器](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. 使用 Dropbox 帐户凭据登录，并授权 Azure 逻辑应用访问你的 Dropbox 数据。 

1. 提供触发器的所需信息。

   ![Dropbox 触发器](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>添加操作

1. 在触发器下，选择“下一步”。 在搜索框中，输入“文件系统”作为筛选器。 在操作列表中选择以下操作：“创建文件 - 文件系统”

   ![查找文件系统连接器](media/logic-apps-using-file-connector/find-file-system-action.png)

1. 如果还没有到文件系统的连接，系统会提示创建连接。

   ![创建连接](media/logic-apps-using-file-connector/file-system-connection.png)

   | 属性 | 必选 | 值 | Description | 
   | -------- | -------- | ----- | ----------- | 
   | **连接名称** | 是 | <connection-name> | 连接使用的名称 | 
   | **根文件夹** | 是 | <*root-folder-name*> | 文件系统的根文件夹，例如，安装本地数据网关的计算机上的某个本地文件夹，或者计算机可访问的网络共享的文件夹。 <p>例如： `\\PublicShare\\DropboxFiles` <p>根文件夹是主要的父文件夹，用作所有与文件有关的操作的相对路径。 | 
   | **身份验证类型** | 否 | <*auth-type*> | 文件系统使用的身份验证类型，例如 **Windows** | 
   | **用户名** | 是 | <*domain*>\\<*username*> | 以前安装的数据网关的用户名 | 
   | **密码** | 是 | <*your-password*> | 以前安装的数据网关的密码 | 
   | **gateway** | 是 | <*installed-gateway-name*> | 以前安装的网关的名称 | 
   ||| 

1. 完成后，选择“创建”。 

   逻辑应用会配置和测试连接，以确保连接正常运行。 
   如果正确建立连接，会显示用于之前所选操作的选项。 

1. 在“创建文件”操作中，提供用于将文件从 Dropbox 复制到本地文件共享中的根文件夹的详细信息。 若要添加前面步骤的输出，请在框中单击，然后在显示动态内容列表时从可用字段中进行选择。

   ![创建文件操作](media/logic-apps-using-file-connector/create-file-filled.png)

1. 现在添加一个发送电子邮件的 Outlook 操作，使相应的用户知道已创建新文件。 输入收件人、标题和电子邮件正文。 若要测试，可以使用自己的电子邮件地址。

   ![发送电子邮件操作](media/logic-apps-using-file-connector/send-email.png)

1. 保存逻辑应用。 通过将文件上传到 Dropbox 来测试应用。 

   逻辑应用应会将该文件复制到本地文件共享，并向收件人发送一封有关已复制的文件的电子邮件。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/fileconnector/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 为了帮助我们改进 Azure 逻辑应用和连接器，请在 [Azure 逻辑应用用户之声网站](https://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

* 了解如何[连接到本地数据](../logic-apps/logic-apps-gateway-connection.md) 
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
