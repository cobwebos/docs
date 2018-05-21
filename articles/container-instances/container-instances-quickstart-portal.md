---
title: 快速入门 - 使用 Azure 门户创建首个 Azure 容器实例容器
description: 在本快速入门中，我们将使用 Azure 门户在 Azure 容器实例中部署一个容器
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入门：在 Azure 容器实例中创建第一个容器

使用 Azure 容器实例，可以在 Azure 中轻松创建和管理 Docker 容器，无需预配虚拟机或采用更高级别的服务。 在本快速入门中，将使用 Azure 门户在 Azure 中创建容器，再通过完全限定的域名 (FQDN) 向 Internet 公开该容器。 配置一些设置后，你将在浏览器中看到以下内容：

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-portal-07]

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][azure-free-account]。

## <a name="create-a-container-instance"></a>创建容器实例

选择“创建资源” > “容器” > “容器实例”。

![开始在 Azure 门户中创建新的容器实例][aci-portal-01]

在“容器名称”、“容器映像”和“资源组”文本框中输入以下值。 将其他值保留为默认值，然后选择“确定”。

* 容器名称：`mycontainer`
* 容器映像：`microsoft/aci-helloworld`
* 资源组：`myResourceGroup`

![在 Azure 门户中配置新的容器实例的基本设置][aci-portal-03]

可以在 Azure 容器实例中创建 Windows 和 Linux 容器。 对于本快速入门，请保留默认设置 **Linux** 来部署基于 Linux 的 `microsoft/aci-helloworld` 映像。

在“配置”下，为容器指定一个 **DNS 名称标签**。 该名称在创建容器实例的 Azure 区域中必须是唯一的。 容器将可通过 `<dns-name-label>.<region>.azurecontainer.io` 公开访问。

将“配置”中的其他设置保留为各自的默认设置，然后选择“确定”来验证配置。

![在 Azure 门户中配置新的容器实例][aci-portal-04]

完成验证后，将显示容器的设置摘要。 选择“确定”提交容器部署请求。

![Azure 门户中新的容器实例的设置摘要][aci-portal-05]

当部署启动时，会在门户仪表板上显示一个磁贴，指明部署正在进行。 在部署后，该磁贴将显示新的容器实例。

![Azure 门户中新的容器实例的创建进度][aci-portal-08]

选择“mycontainer”容器实例来显示其属性。 记下容器实例的 **FQDN**（完全限定的域名）及其**状态**。

![Azure 门户中的容器组概述][aci-portal-06]

在其**状态**为“正在运行”后，在浏览器中导航到容器的 FQDN。

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-portal-07]

祝贺你！ 仅仅配置了几个设置，你便已在 Azure 容器实例中部署了一个可供公开访问的应用程序。

## <a name="view-container-logs"></a>查看容器日志

当排查容器或其运行的应用程序的问题时，查看容器实例的日志非常有用。

若要查看容器的日志，请在“设置”下选择“容器”，然后选择“日志”。 应当会看到在浏览器中查看应用程序时生成的 HTTP GET 请求。

![Azure 门户中的容器日志][aci-portal-11]

## <a name="clean-up-resources"></a>清理资源

完成容器后，选择 *mycontainer* 容器实例的“概述”，然后选择“删除”。

![在 Azure 门户中删除容器实例][aci-portal-09]

在出现确认对话框时选择“是”。

![Azure 门户中容器实例的删除确认][aci-portal-10]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已基于公共 Docker 中心注册表中的映像创建了 Azure 容器实例。 若要亲自基于专用 Azure 容器注册表生成容器映像并将其部署到 Azure 容器实例，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/