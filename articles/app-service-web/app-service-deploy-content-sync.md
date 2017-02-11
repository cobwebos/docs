---
title: "将内容从云文件夹同步到 Azure 应用服务"
description: "了解如何通过内容同步，将应用从云文件夹部署到 Azure 应用服务。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ddda0e60dc9d5414142791175b77a5a65e3f40e3


---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>将内容从云文件夹同步到 Azure 应用服务
本教程说明如何通过从流行的云存储服务（例如 Dropbox 与 OneDrive）同步内容，来部署到 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)。 

## <a name="a-nameoverviewaoverview-of-content-sync-deployment"></a><a name="overview"></a>内容同步部署概述
按需内容同步部署的后盾是与应用服务集成的 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki)。 在 [Azure 门户](https://portal.azure.com)上，可以在云存储中指定一个文件夹，然后使用该文件夹中的应用代码和内容，并通过单击按钮同步到应用服务。 内容同步利用 Kudu 进程来完成生成和部署。 

## <a name="a-namecontentsyncahow-to-enable-content-sync-deployment"></a><a name="contentsync"></a>如何启用内容同步部署
若要从 [Azure 门户](https://portal.azure.com)启用内容同步，请遵循以下步骤：

1. 在 Azure 门户的应用边栏选项卡中，单击**“设置”** > **“部署源”**。 单击“选择源”，然后选择“OneDrive”或“Dropbox”作为部署源。 
   
    ![内容同步](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > 由于 API 中存在的基础差异，目前不支持 **OneDrive for Business**。 
   > 
   > 
2. 完成授权工作流，以便应用服务能够访问存储所有应用服务内容的 OneDrive 或 Dropbox 的特定预定义路径。  
    授权后，应用服务平台将提供在指定内容路径下创建内容文件夹的选项，或者选择此指定的内容路径下的现有内容文件夹。 下面是用于应用服务同步的云存储帐户下的指定内容路径：  
   
   * **OneDrive**：`Apps\Azure Web Apps` 
   * **Dropbox**：`Dropbox\Apps\Azure`
3. 完成初始内容同步后，内容同步可以按需从 Azure 门户启动。 “部署”边栏选项卡提供了部署历史记录。
   
    ![部署历史记录](./media/app-service-deploy-content-sync/onedrive_sync.png)

[从 Dropbox 部署](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)中提供了有关 Dropbox 部署的详细信息。 




<!--HONumber=Nov16_HO3-->


