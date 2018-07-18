---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ae84b5fd5647ef6c54006c0411e334b14173392e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33836431"
---
1. 导航到[“Google Cloud Console”](https://console.developers.google.com/cloud-resource-manager)，然后使用 Google 帐户凭据登录。 
2. 在工具栏中选择“创建项目”。 
   
    ![创建新项目](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. 在“项目名称”中，输入项目名称，然后单击“创建”。
4. 选择工具栏上的“警报”按钮，然后在列表中选择项目。 随即显示项目的仪表板。 也可以通过使用以下 URL 直接导航到仪表板：https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>

    ![在警报中选择项目](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. 记下仪表板中“项目信息”磁贴中的“项目编号”。 

    ![项目 ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. 在仪表板中的“API”磁贴上，选择“转到 API 概述”。 

    ![API 概述链接](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. 在“API”页上，选择“启用 API 和服务”。 

    ![“启用 API 和服务”按钮](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. 搜索并选择“Google Cloud Messaging”。 

    ![搜索并选择“Google Cloud Messaging”](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. 若要为项目启用 Google Cloud Messaging，请选择“启用”。

    ![启用 Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. 选择工具栏上的“创建凭据”。 

    ![“创建凭据”按钮](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. 在“向项目添加凭据”页上，选择“API 密钥”链接。 

    ![“创建凭据”按钮](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. 在“API 密钥”页上，选择“创建/保存”。 在以下示例中，已选中“IP 地址”选项并输入“0.0.0.0/0”作为允许的 IP 地址。 应该对 API 密钥进行相应的限制。 

    ![API 密钥 - “创建”按钮](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. 将“API 密钥”复制到剪贴板并保存在其他位置。 

    ![复制 API 密钥](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    接下来，将使用此 API 密钥值，让 Azure 对 GCM 进行身份验证并代表应用程序发送推送通知。 若要导航回项目仪表板，请使用以下 URL：https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>

