<!-- not suitable for Mooncake -->

<properties
	pageTitle="将内容从云文件夹中同步到 Azure Web 应用"
	description="了解如何通过内容同步，将应用从云文件夹部署到 Azure。"
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.date="03/18/2016"
	wacn.date=""/>
    
# 将云文件夹中的内容同步到 Azure

部署到 [Azure Web 应用](/documentation/services/web-sites/)的选项之一是从流行的云存储服务（例如 Dropbox 与 OneDrive）同步内容。

## <a name="overview"></a>内容同步部署概述

按需内容同步部署的后盾是与 Azure Web 应用集成的 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki)。在 [Azure 管理门户](https://manage.windowsazure.cn)上，你可以在云存储中指定一个文件夹，然后使用该文件夹中的应用代码和内容，并通过单击按钮同步到 Azure。内容同步利用 Kudu 进程来完成生成和部署。
    
## <a name="contentsync"></a>如何启用内容同步部署
若要从 [Azure 管理门户](https://manage.windowsazure.cn)启用内容同步，请遵循以下步骤：

1. 在 Azure 管理门户上的应用边栏选项卡中，单击“设置”>“部署源”。单击“选择源”，然后选择“OneDrive”或“Dropbox”作为部署源。 

    ![内容同步](./media/app-service-deploy-content-sync/deployment_source.png)

2. 完成授权工作流，以便 Azure 能够访问存储所有 App Service 内容的 OneDrive 或 Dropbox 的特定预定义路径。  
    授权后，Azure 平台将提供在指定内容路径下创建内容文件夹的选项，或者选择此指定的内容路径下的现有内容文件夹。下面是用于 Azure 同步的云存储帐户下的指定内容路径：
    * **OneDrive**：`Apps\Azure Web Apps` 
    * **Dropbox**：`Dropbox\Apps\Azure`

3. 完成初始内容同步后，内容同步可以按需从 Azure 管理门户启动。“部署”边栏选项卡提供了部署历史记录。

    ![部署历史记录](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
[从 Dropbox 部署](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)中提供了有关 Dropbox 部署的详细信息。



<!---HONumber=Mooncake_0328_2016-->