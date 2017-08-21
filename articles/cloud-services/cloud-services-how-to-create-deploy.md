---
title: "如何创建和部署云服务 | Microsoft Docs"
description: "了解如何在 Azure 中使用“快速创建”方法创建和部署云服务。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 2a2172a78bfd3ac923edbc9de366b035629dd27b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>如何创建和部署云服务
> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-how-to-create-deploy-portal.md)
> * [Azure 经典门户](cloud-services-how-to-create-deploy.md)
> 
> 

Azure 经典门户提供两种创建和部署云服务的方法：**快速创建**和**自定义创建**。

本主题介绍如何使用“快速创建”方法创建新的云服务，然后使用“上传”在 Azure 中上传和部署云服务包。 使用此方法时，Azure 经典门户将在用户操作时提供方便的链接以便满足所有要求。 如果可以在创建云服务时对其进行部署，可使用“自定义创建”同时执行这两项操作。

> [!NOTE]
> 如果计划从 Visual Studio Team Services (VSTS) 发布云服务，请使用“快速创建”，并从“快速启动”或仪表板设置 VSTS 发布。
> 
> 

## <a name="concepts"></a>概念
要将应用程序部署为 Azure 中的云服务，需要以下三个组件：

* **服务定义**  
  云服务定义文件 (.csdef) 可定义服务模型，包括角色数量。
* **服务配置**  
  云服务配置文件 (.cscfg) 提供云服务和各个角色的配置设置，包括角色实例的数量。
* **服务包**  
  服务包 (.cspkg) 包含应用程序代码和配置以及服务定义文件。

你可以通过[此处](cloud-services-model-and-package.md)了解有关这些内容以及如何创建包的详细信息。

## <a name="prepare-your-app"></a>准备应用程序
在可以部署云服务之前，必须根据你的应用程序代码创建云服务包 (.cspkg)，并创建云服务配置文件 (.cscfg)。 Azure SDK 提供了用于准备这些必需的部署文件的工具。 你可以从 [Azure 下载](https://azure.microsoft.com/downloads/)页安装 SDK，并使用你选择用于开发应用程序代码的语言。

在你导出服务包之前，三种云服务功能需要特殊的配置：

* 如果你要部署使用安全套接字层 (SSL) 进行数据加密的云服务，请[为应用程序配置](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) SSL。
* 如果要配置与角色实例的远程桌面连接，请[为这些角色配置](cloud-services-role-enable-remote-desktop.md)远程桌面。
* 如果要为云服务配置详细监视，请为云服务启用 Azure 诊断。 *最少监视*（默认监视级别）使用从角色实例（虚拟机）的主机操作系统中收集到的性能计数器。 “详细监视”*收集基于角色实例中性能数据的其他度量信息，以便对应用程序处理期间出现的问题进行进一步分析。 若要了解如何启用 Azure 诊断，请参阅[在 Azure 中启用诊断](cloud-services-dotnet-diagnostics.md)。

要使用 Web 角色或辅助角色创建云服务，你必须[创建服务包](cloud-services-model-and-package.md#servicepackagecspkg)。

## <a name="before-you-begin"></a>开始之前
* 如果你尚未安装 Azure SDK，请单击“**安装 Azure SDK**”以打开 [Azure 下载页](https://azure.microsoft.com/downloads/)，然后下载你选择用于开发代码的相应语言的 SDK。 （你也可以稍后执行此操作。）
* 如果任何角色实例需要证书，请创建这些证书。 云服务需要带有私钥的 .pfx 文件。 可在创建和部署云服务时[将这些证书上传到 Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate)。
* 如果你计划将云服务部署到关联组，请创建关联组。 可以使用关联组将你的云服务和其他 Azure 服务部署到某个区域中的同一位置。 你可以在 Azure 经典门户的“**网络**”区域中的“**地缘组**”页上创建地缘组。

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>如何：使用“快速创建”创建云服务
1. 在 [Azure 经典门户](http://manage.windowsazure.com/)中，单击“新建”>“计算”>“云服务”>“快速创建”。
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. 在 **URL** 中，输入要在公用 URL 中使用的子域名称，以便在生产部署中访问云服务。 生产部署的 URL 格式为：http://*myURL*.cloudapp.net。
3. 在“区域或地缘组”中，选择要在其中部署云服务的地理区域或地缘组。 若要将云服务和其他 Azure 服务部署到某个区域中的同一位置，请选择地缘组。
4. 单击“创建云服务” 。
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    可以在窗口底部的消息区域中监视过程的状态。
   
    “云服务”区域随即打开，显示新的云服务。 状态更改为“已创建”时，即成功完成云服务创建。
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>如何：为云服务上传证书
1. 在 [Azure 经典门户](http://manage.windowsazure.com/)中单击“云服务”，单击云服务的名称，然后单击“证书”。
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. 单击“上传证书”或“上传”。
3. 在“文件”中，使用“浏览”选择证书（.pfx 文件）。
4. 在“密码”中，输入证书的私钥。
5. 单击“**确定**”（复选标记）。
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    可在消息区域中查看上传进度，如下所示。 上传完成时，证书即添加到表中。 在消息区域中，单击“确定”关闭消息。
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>如何：部署云服务
1. 在 [Azure 经典门户](http://manage.windowsazure.com/)中，依次单击“云服务”、云服务名称、“仪表板”。
2. 单击“上传新的生产部署”或“上传”。
3. 在“部署标签”中，输入新部署的名称 - 例如 MyCloudServicev4。
4. 在“包”中，使用“浏览”选择要使用的服务包文件 (.cspkg)。
5. 在“配置”中，使用“浏览”选择要使用的服务配置文件 (.cscfg)。
6. 如果云服务将包括只具有一个实例的任何角色，请选中“即使一个或多个角色包含单个实例也进行部署”复选框以使部署继续进行。
   
    如果每个角色至少具有两个实例，则在维护和服务更新期间，Azure 只能保证 99.95% 的云服务访问。 如果需要，可以在部署云服务后，在“缩放”页上添加其他角色实例。 有关详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)。
7. 单击“确定”（复选标记）以开始云服务部署。
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    可以在消息区域中监视部署状态。 单击“确定”隐藏消息。
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>验证确认你的部署已成功完成
1. 单击“仪表板”。
   
    该状态应该显示该服务“**正在运行**”。
2. 在“速览”下，单击网站 URL 以在 Web 浏览器中打开云服务。
   
    ![云服务_速览](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


## <a name="next-steps"></a>后续步骤
* [云服务的常规配置](cloud-services-how-to-configure.md)。
* 配置[自定义域名](cloud-services-custom-domain-name.md)。
* [管理云服务](cloud-services-how-to-manage.md)。
* 配置 [SSL 证书](cloud-services-configure-ssl-certificate.md)。


