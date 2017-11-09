---
title: "将现有应用快速部署到 Azure Service Fabric 群集"
description: "通过 Visual Studio 使用 Azure Service Fabric 群集来托管现有的 Node.js 应用程序。"
services: service-fabric
documentationcenter: nodejs
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/13/2017
ms.author: adegeo
ms.openlocfilehash: f63cf0baf7636d0294ced7c2dc6b3112fa9900ee
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>在 Azure Service Fabric 上托管 Node.js 应用程序

本快速入门教程帮助你将现有的应用程序（本示例中为 Node.js）部署到在 Azure 上运行的 Service Fabric 群集。

## <a name="prerequisites"></a>先决条件

开始之前，请确保已 [设置开发环境](service-fabric-get-started.md)。 其中包括安装 Service Fabric SDK 和 Visual Studio 2017 或 2015。

此外还需一个用于部署的现有 Node.js 应用程序。 本快速入门教程使用简单的 Node.js 网站，可从[此处][download-sample]下载。 在下一步骤中，请在创建项目后将此文件提取到 `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` 文件夹。

如果还没有 Azure 订阅，请创建一个[免费帐户][create-account]。

## <a name="create-the-service"></a>创建服务

以管理员身份启动 Visual Studio。

使用 `CTRL`+`SHIFT`+`N` 创建一个项目

在“新建项目”对话框中，选择“云”>“Service Fabric 应用程序”。

将应用程序命名为“MyGuestApp”，然后按“确定”。

>[!IMPORTANT]
>Node.js 可能会轻松突破针对 Windows 路径的 260 字符限制。 请对项目本身使用短路径，例如 c:\code\svc1。 （可选）可以按照[这些说明](https://stackoverflow.com/a/41687101/1664231)在 Windows 10 中启用长文件路径。
   
![Visual Studio 中的新建项目对话框][new-project]

可以在下一对话框中创建任何类型的 Service Fabric 服务。 对于本快速入门教程，请选择“来宾可执行文件”。

将服务命名为“MyGuestService”，然后将右侧的选项设置为以下值：

| 设置                   | 值 |
| ------------------------- | ------ |
| 代码包文件夹       | _&lt;包含 Node.js 应用的文件夹&gt;_ |
| 代码包行为     | 将文件夹内容复制到项目 |
| 节目                   | node.exe |
| 参数                 | server.js |
| 工作文件夹            | CodePackage |

按“确定”。

![Visual Studio 中的新建服务对话框][new-service]

Visual Studio 会创建应用程序项目和执行组件服务项目，并在解决方案资源管理器中显示它们。

应用程序项目 (MyGuestApp) 不直接包含任何代码。 而是引用一组服务项目。 此外，它包含三种其他类型的内容：

* 发布配置文件  
针对不同环境的工具首选项。

* 脚本  
用于部署/升级应用程序的 PowerShell 脚本。

* 应用程序定义  
包括 ApplicationPackageRoot 下的应用程序清单。 关联应用程序参数文件位于 *ApplicationParameters* 下，它们定义应用程序并使你可以专门为给定环境对其进行配置。
    
有关服务项目的内容概述，请参阅 [Reliable Services 入门](service-fabric-reliable-services-quick-start.md)。

## <a name="set-up-networking"></a>设置网络

要部署的示例 Node.js 应用使用端口 80，我们需告知 Service Fabric：我们需公开该端口。

打开项目中的 ServiceManifest.xml 文件。 在清单底部，有一个已定义条目的 `<Resources> \ <Endpoints>`。 修改该条目，添加 `Port`、`Protocol` 和 `Type`。 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>部署到 Azure

如果通过按 F5 来运行项目，则会将其部署到本地群集。 但请将其部署到 Azure。

右键单击项目，选择“发布...”，此时会打开一个将内容发布到 Azure 所需的对话框。

![用于 Service Fabric 服务的“发布到 Azure”对话框][publish]

选择 PublishProfiles\Cloud.xml 目标配置文件。

选择要将内容部署到其中的 Azure 帐户（如果尚未这样做）。 如果还没有该帐户，请[在此注册一个][create-account]。

在“连接终结点”下，选择要将内容部署到其中的 Service Fabric 群集。 如果没有该群集，请选择**&lt;新建群集...&gt;**，此时会打开通往 Azure 门户的 Web 浏览器窗口。 有关详细信息，请参阅[在门户中创建群集](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)。 

创建 Service Fabric 群集时，请确保将“自定义终结点”设置设为“80”。

![终结点为自定义的 Service Fabric 节点类型配置][custom-endpoint]

创建新的 Service Fabric 群集需要一段时间来完成。 创建完以后，请回到发布对话框，然后选择**&lt;刷新&gt;**。 新群集列在下拉列表框中；请将其选中。

按“发布”，然后等待部署完成。

这可能需要几分钟的时间。 完成后，可能还需要等待几分钟，该应用程序才会完全可用。

## <a name="test-the-website"></a>测试网站

发布服务以后，请在 Web 浏览器中对其进行测试。 

首先，打开 Azure 门户并找到 Service Fabric 服务。

检查服务地址的概览边栏选项卡。 使用“客户端连接终结点”属性中的域名。 例如，`http://mysvcfab1.westus2.cloudapp.azure.com`。

![Azure 门户中的 Service Fabric 概览边栏选项卡][overview]

导航到该地址，可以在其中看到 `HELLO WORLD` 响应。

## <a name="delete-the-cluster"></a>删除群集

请勿忘记删除为本快速入门教程创建的所有资源，因为系统会对这些资源收费。

## <a name="next-steps"></a>后续步骤
阅读更多有关[来宾可执行文件](service-fabric-deploy-existing-app.md)的内容。

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F