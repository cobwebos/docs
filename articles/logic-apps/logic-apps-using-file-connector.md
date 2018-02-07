---
title: "连接到本地文件系统 - Azure 逻辑应用 | Microsoft Docs"
description: "通过本地数据网关和文件系统连接器从逻辑应用工作流连接到本地文件系统"
keywords: "文件系统, 本地"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 32ab5be41a8dee3b1f2c0b1bde076c0d1a844bdd
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>使用文件系统连接器从逻辑应用连接到本地文件系统

为了管理数据并安全访问本地资源，逻辑应用可使用本地数据网关。 本文介绍如何通过基本示例方案连接到本地文件系统：将上传至 Dropbox 的文件复制到文件共享，然后发送一封电子邮件。

## <a name="prerequisites"></a>先决条件

* 下载最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)。

* 安装和设置最新的本地数据网关（版本 1.15.6150.1 或更高版本）。 有关步骤，请参阅[连接到本地数据源](http://aka.ms/logicapps-gateway)。 必须先在本地计算机上安装网关，才能继续执行这些步骤。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>添加用于连接到文件系统的触发器和操作

1. 创建空白逻辑应用。 将此触发器添加为第一步：“Dropbox - 创建文件后” 

2. 在该触发器下，选择“+ 下一步” > “添加操作”。 

3. 在搜索框中，输入“文件系统”作为筛选器。 看到文件系统连接器的所有操作时，选择“文件系统 - 创建文件”操作。 

   ![搜索“文件连接器”](media/logic-apps-using-file-connector/search-file-connector.png)

4. 如果还没有到文件系统的连接，系统会提示创建连接。 

5. 选择“通过本地数据网关连接”。 显示连接属性时，请按表中所示设置连接。

   ![配置连接](media/logic-apps-using-file-connector/create-file.png)

   | 设置 | 说明 |
   | ------- | ----------- |
   | **根文件夹** | 指定文件系统的根文件夹。 可以在安装了本地数据网关的计算机上指定本地文件夹，或者该文件夹可以是计算机可访问的网络共享。 <p>提示：根文件夹是主要的父文件夹，用作所有与文件有关的操作的相对路径。 | 
   | **身份验证类型** | 文件系统所用的身份验证类型 | 
   | **用户名** | 提供之前安装的网关的用户名 {domain\\username}。 | 
   | **密码** | 提供之前安装的网关的密码。 | 
   | **网关** | 选择之前安装的网关。 | 
   ||| 

6. 提供所有连接详细信息后，选择“创建”。 

   逻辑应用会配置和测试连接，以确保连接正常运行。 
   如果正确建立连接，会显示用于之前所选操作的选项。 
   现在，文件系统连接器可供使用。

7. 设置“创建文件”操作，用于将文件从 Dropbox 复制到本地文件共享的根文件夹。

   ![创建文件操作](media/logic-apps-using-file-connector/create-file-filled.png)

8. 使用此操作复制文件后，请添加一个 Outlook 操作，该操作发送一封电子邮件，以便相关用户知晓此新文件。 输入收件人、标题和电子邮件正文。 

   在“动态内容”列表中，可从文件连接器选择数据输出，以便向电子邮件添加更多详细信息。

   ![发送电子邮件操作](media/logic-apps-using-file-connector/send-email.png)

9. 保存逻辑应用。 通过将文件上传到 Dropbox 来测试应用。 该文件应复制到本地文件共享，并且应该会收到有关该操作的电子邮件。

恭喜，现在已具有一个可连接到本地文件系统的正在运行的逻辑应用。 

请尝试探索连接器提供的其他功能，例如：

- 创建文件
- 列出文件夹中的文件
- 附加文件
- 删除文件
- 获取文件内容
- 使用路径获取文件内容
- 获取文件元数据
- 使用路径获取文件元数据
- 列出根文件夹中的文件
- 更新文件

## <a name="view-the-swagger"></a>查看 Swagger

请参阅 [Swagger 详细信息](/connectors/fileconnector/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 为了帮助我们改进 Azure 逻辑应用和连接器，请在 [Azure 逻辑应用用户之声网站](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

* [连接到本地数据](../logic-apps/logic-apps-gateway-connection.md) 
* [监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [适用于 B2B 方案的企业集成](../logic-apps/logic-apps-enterprise-integration-overview.md)
