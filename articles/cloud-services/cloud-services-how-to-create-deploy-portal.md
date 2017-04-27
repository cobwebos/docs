---
title: "如何创建和部署云服务 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建和部署云服务。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 542817c562e10ff32c62afd186cf012abbe82ac5
ms.lasthandoff: 04/20/2017


---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>如何创建和部署云服务
> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-how-to-create-deploy-portal.md)
> * [Azure 经典门户](cloud-services-how-to-create-deploy.md)
>
>

Azure 门户为你提供两种创建和部署云服务的方法：*快速创建*和*自定义创建*。

本文介绍如何使用“快速创建”方法创建新的云服务，然后使用“**上载**”在 Azure 中上载和部署云服务包。 使用此方法时，Azure 门户在你进行操作时将提供方便的链接供你完成所有要求。 如果你在创建云服务时还准备部署该云服务，则可以使用“自定义创建”同时执行这两项操作。

> [!NOTE]
> 如果你计划从 Visual Studio Team Services (VSTS) 发布云服务，请使用“快速创建”，然后从“Azure 快速启动”或仪表板设置 VSTS 发布。 有关详细信息，请参阅[使用 Visual Studio Team Services 向 Azure 持续交付][TFSTutorialForCloudService]，或查看“快速启动”页的帮助。
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

* 如果你要部署使用安全套接字层 (SSL) 进行数据加密的云服务，请[为应用程序配置](cloud-services-configure-ssl-certificate-portal.md#modify) SSL。
* 如果要配置与角色实例的远程桌面连接，请[为这些角色配置](cloud-services-role-enable-remote-desktop.md)远程桌面。 这只能在经典门户中进行。
* 如果要为云服务配置详细监视，请为云服务启用 Azure 诊断。 *最少监视*（默认监视级别）使用从角色实例（虚拟机）的主机操作系统中收集到的性能计数器。 *详细监视*根据角色实例中的性能数据收集其他度量信息，以便对处理应用程序期间出现的问题做进一步分析。 若要了解如何启用 Azure 诊断，请参阅[在 Azure 中启用诊断](cloud-services-dotnet-diagnostics.md)。

要使用 Web 角色或辅助角色创建云服务，你必须[创建服务包](cloud-services-model-and-package.md#servicepackagecspkg)。

## <a name="before-you-begin"></a>开始之前
* 如果你尚未安装 Azure SDK，请单击“**安装 Azure SDK**”以打开 [Azure 下载页](https://azure.microsoft.com/downloads/)，然后下载你选择用于开发代码的相应语言的 SDK。 （你也可以稍后执行此操作。）
* 如果任何角色实例需要证书，请创建这些证书。 云服务需要带有私钥的 .pfx 文件。 你可以在创建和部署云服务时[将这些证书上载到 Azure]()。
* 如果你计划将云服务部署到关联组，请创建关联组。 可以使用关联组将你的云服务和其他 Azure 服务部署到某个区域中的同一位置。 你可以在 Azure 经典门户的“**网络**”区域中的“**地缘组**”页上创建地缘组。

## <a name="create-and-deploy"></a>创建和部署
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“新建”>“计算”，然后向下滚动到“云服务”并单击它。

    ![发布云服务](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. 在显示的信息页面底部，单击“**创建**”。
4. 在新的“**云服务**”边栏选项卡中，输入“**DNS 名称**”的值。
5. 创建一个新“**资源组**”或选择一个现有的资源组。
6. 选择“位置” 。
7. 单击“**包**”。 此时会打开“**上载包**”边栏选项卡。 填写必填字段。 如果你的任何角色包含单个实例，请确保选中“**即使一个或多个角色包含单个实例也进行部署**”。

    > [!IMPORTANT]
    > 云服务仅可与[经典存储帐户](../azure-resource-manager/resource-manager-deployment-model.md)关联。 如果出现一条消息，显示针对你的订阅和位置未找到任何存储帐户，请确保已在该位置为云服务创建了经典存储帐户。

8. 请确保选中“**开始部署**”。
9. 单击“**确定**”，此时会关闭“**上载包**”边栏选项卡。
10. 如果不添加任何证书，请单击“**创建**”。

    ![发布云服务](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>上载证书
如果你的部署包已[配置为使用证书](cloud-services-configure-ssl-certificate-portal.md#modify)，你现在就可以上载证书。

1. 选择“**证书**”，并在“**添加证书**”边栏选项卡中，选择 SSL 证书 .pfx 文件，然后提供证书的“**密码**”，
2. 单击“**附加证书**”，然后单击“**添加证书**”边栏选项卡上的“**确定**”。
3. 单击“**云服务**”边栏选项卡上的“**创建**”。 当部署达到“**就绪**”状态时，你可以继续执行后续步骤。

    ![发布云服务](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>验证确认你的部署已成功完成
1. 单击云服务实例。

    该状态应该显示该服务“**正在运行**”。
2. 在“**必备**”下，单击“**站点 URL**”以在 Web 浏览器中打开你的云服务。

    ![云服务_速览](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>后续步骤
* [云服务的常规配置](cloud-services-how-to-configure-portal.md)。
* 配置[自定义域名](cloud-services-custom-domain-name-portal.md)。
* [管理云服务](cloud-services-how-to-manage-portal.md)。
* 配置 [SSL 证书](cloud-services-configure-ssl-certificate-portal.md)。

