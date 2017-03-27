---
title: "连接到 Azure 逻辑应用中的本地文件系统文件夹 | Microsoft 文档"
description: "使用本地数据网关连接到逻辑应用工作流中的本地文件系统"
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 336e3f2bba2fe3bb7abdd6959354349ff61ed6a8
ms.openlocfilehash: 3bbcb5e9c7a4731eb8eb410a94fddb93b2ea8416


---
# <a name="use-the-file-system-connector-with-the-on-premises-data-gateway-in-a-logic-app"></a>在逻辑应用中将文件系统连接器与本地数据网关配合使用

混合云连接是逻辑应用的核心。 通过本地数据网关可管理数据并从逻辑应用安全访问本地资源。 本文将介绍如何通过简单的方案连接到本地文件系统：将上传到 Dropbox 的文件复制到文件共享，然后发送一封电子邮件。

## <a name="prerequisites"></a>先决条件
- 安装并配置最新的[本地数据网关](https://www.microsoft.com/en-us/download/details.aspx?id=53127)。
- 安装最新的本地数据网关版本 1.15.6150.1 或更高版本。 [连接到本地数据网关](http://aka.ms/logicapps-gateway)中列出了相关步骤。 网关必须先安装在本地计算机上，然后你才能继续执行剩余步骤。

## <a name="use-file-system-connector"></a>使用文件系统连接器

1. 创建一个名为“创建文件时”的 Dropbox 触发器，然后只需在搜索中键入“文件系统”即可查看所有受支持的文件连接器操作。

 ![搜索“文件连接器”](media/logic-apps-using-file-connector/search-file-connector.png)

2. 选择“创建文件”，并为其创建连接。
 - 如果没有现有连接，系统会提示创建连接。
 - 选中“通过本地数据网关连接”选项。 将显示更多属性。
 - 选择根文件夹。 根文件夹可以是计算机上本地数据网关安装到的本地文件夹，也可以是计算机有权访问的网络共享。
 - 输入网关的用户名和密码。
 - 选择上一步中安装的网关。
    
 > [!NOTE]
 > 根文件夹是主要的父文件夹，用作所有与文件有关的操作的相对路径。

 ![配置连接](media/logic-apps-using-file-connector/create-file.png)

3. 提供所有详细信息后，单击“创建”。 逻辑应用将配置并测试连接，确保其可正常工作。 检查所有项目后，会出现前面选择的卡的相应选项。 现在，文件系统连接器可供使用。

4. 选择上传至 Dropbox 的文件，并将其复制到位于本地的文件共享的根文件夹。

 ![创建文件操作](media/logic-apps-using-file-connector/create-file-filled.png)

5. 复制文件后，发送电子邮件通知相关用户。 与其他连接器相同，可在“动态内容”选择器中使用上一操作的输出。
 - 输入收件人、标题和电子邮件正文。 使用“动态内容”选择器从文件连接器选择输出，丰富电子邮件内容。

 ![发送电子邮件操作](media/logic-apps-using-file-connector/send-email.png)

6. 保存逻辑应用，并通过上传文件到 Dropbox 对其进行测试。 应看到复制到本地文件共享的文件，且会收到有关该操作的电子邮件通知。

    > [!TIP] 
    > 查看如何[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

7. 所有操作完成后，使用文件系统连接器的逻辑应用正常运行。 可开始浏览其提供的其他功能：

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

## <a name="next-steps"></a>后续步骤
- 了解 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)。 
- 创建逻辑应用的[本地连接](../logic-apps/logic-apps-gateway-connection.md)。



<!--HONumber=Jan17_HO5-->


