---
title: 将内容从云文件夹同步到 Azure 应用服务
description: 了解如何通过内容同步，将应用从云文件夹部署到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 3781010c74daa51c92813db85ee03eaa4c02a4cf
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233581"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>将内容从云文件夹同步到 Azure 应用服务
本文介绍如何将内容同步到 Dropbox 和 OneDrive 上的 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)。 

按需内容同步部署由应用服务的 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki)提供支持。 你可以在指定的云文件夹中处理应用代码和内容，然后通过单击按钮将其同步到应用服务。 内容同步使用 Kudu 生成服务器。 

## <a name="enable-content-sync-deployment"></a>启用内容同步部署

若要启用内容同步，请导航至 [Azure 门户](https://portal.azure.com)中的“应用服务应用”页。

在左侧菜单中，单击“部署中心” > “OneDrive”或“Dropbox” > “授权”。 按照授权提示进行操作。 

![](media/app-service-deploy-content-sync/choose-source.png)

只需使用 OneDrive 或 Dropbox 授权一次。 如果已获得授权，只需单击“继续”即可。 可以通过单击“更改帐户”来更改已获得授权的 OneDrive 或 Dropbox 帐户。

![](media/app-service-deploy-content-sync/continue.png)

在“配置”页上，选择要同步的文件夹。 将在 OneDrive 或 Dropbox 中的以下指定内容路径下创建此文件夹。 
   
* **OneDrive**：`Apps\Azure Web Apps`
* **Dropbox**：`Apps\Azure`

完成后，单击“继续”。

在“摘要”页中，确认选项，然后单击“完成”。

## <a name="synchronize-content"></a>同步内容

如果想要使用应用服务同步云文件夹中的内容，请返回到“部署中心”页，单击“同步”。

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > 由于 API 中存在的基础差异，目前不支持 **OneDrive for Business**。 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>禁用内容同步部署

若要禁用内容同步，请导航至 [Azure 门户](https://portal.azure.com)中的“应用服务应用”页。

在左侧菜单中，单击“部署中心” > “OneDrive”或“Dropbox” > “断开连接”。

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从本地 Git 存储库进行部署](app-service-deploy-local-git.md)
