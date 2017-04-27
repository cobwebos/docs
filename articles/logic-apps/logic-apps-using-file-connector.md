---
title: "从 Azure 逻辑应用连接到本地文件系统 | Microsoft Docs"
description: "通过本地数据网关和文件系统连接器从逻辑应用工作流连接到本地文件系统"
keywords: "文件系统"
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
ms.date: 01/27/2017
ms.author: deli; LADocs
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 9eab48d40c3d9db896724d3bdb574d89fdab2796
ms.lasthandoff: 04/04/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>使用文件系统连接器从逻辑应用连接到本地文件系统

混合云连接是逻辑应用的核心，因此为了管理数据并安全访问本地资源，逻辑应用可使用本地数据网关。 本文将介绍如何通过基本的方案连接到本地文件系统：将上传到 Dropbox 的文件复制到文件共享，然后发送一封电子邮件。

## <a name="prerequisites"></a>先决条件

- 安装并配置最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)。
- 安装最新的本地数据网关版本 1.15.6150.1 或更高版本。 [连接到本地数据网关](http://aka.ms/logicapps-gateway)中列出了相关步骤。 网关必须先安装在本地计算机上，然后你才能继续执行剩余步骤。

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>添加用于连接到文件系统的触发器和操作

1. 创建一个逻辑应用，然后添加此 Dropbox 触发器：“创建文件时” 
2. 在该触发器下，选择“下一步” > “添加操作”。 
3. 在搜索框中，输入 `file system`，以便可查看所有支持的文件系统连接器操作。

   ![搜索“文件连接器”](media/logic-apps-using-file-connector/search-file-connector.png)

2. 选择“创建文件”操作，并创建到文件系统的连接。

   如果没有现有连接，系统会提示创建连接。

   1. 选择“通过本地数据网关连接”。 将显示更多属性。
   2. 选择文件系统的根文件夹。
      
       > [!NOTE]
       > 根文件夹是主要的父文件夹，用作所有与文件有关的操作的相对路径。 可以在安装了本地数据网关的计算机上指定本地文件夹，或者该文件夹可以是计算机可访问的网络共享。

   3. 输入网关的用户名和密码。
   4. 选择之前安装的网关。

       ![配置连接](media/logic-apps-using-file-connector/create-file.png)

3. 提供所有详细信息后，选择“创建”。 

   逻辑应用会配置和测试连接，以确保连接正常运行。 
   如果正确建立连接，会看到用于之前所选操作的选项。 
   现在，文件系统连接器可供使用。

4. 指定将文件从 Dropbox 复制到本地文件共享的根文件夹。

   ![创建文件操作](media/logic-apps-using-file-connector/create-file-filled.png)

5. 逻辑应用复制文件后，添加一个 Outlook 操作，该操作发送一封电子邮件，以便相关用户知晓此新文件。 输入收件人、标题和电子邮件正文。 

   在动态内容选择器中，可选择文件连接器的数据输出，以便可以向电子邮件添加更多详细信息。

   ![发送电子邮件操作](media/logic-apps-using-file-connector/send-email.png)

6. 保存逻辑应用。 通过将文件上传到 Dropbox 来测试应用。 该文件应复制到本地文件共享，并且应该会收到有关该操作的电子邮件。

   > [!TIP] 
   > 了解如何[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

恭喜，现在已具有一个可连接到本地文件系统的正在运行的逻辑应用。 请尝试探索连接器提供的其他功能，例如：

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

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

- 从逻辑应用[连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
- 了解[企业集成](../logic-apps/logic-apps-enterprise-integration-overview.md)

