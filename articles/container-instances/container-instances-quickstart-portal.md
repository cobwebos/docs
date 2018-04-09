---
title: 快速入门 - 使用 Azure 门户创建首个 Azure 容器实例容器
description: 在本快速入门中，我们将使用 Azure 门户在 Azure 容器实例中部署一个容器
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: cb0c8c5f5730ae1f7a2e9b38c3ef3e04ee8cde67
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入门：在 Azure 容器实例中创建第一个容器

可以通过 Azure 容器实例在 Azure 中轻松创建和管理容器。 在此快速入门中，将在 Azure 中创建容器，再通过公共 IP 地址向 Internet 公开该容器。 通过使用 Azure 门户完成此操作。 只需单击几下，就会在浏览器中看到以下图像：

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-portal-07]

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-container-instance"></a>创建容器实例

选择“创建资源” > “容器” > “Azure 容器实例”。

![开始在 Azure 门户中创建新的容器实例][aci-portal-01]

在“容器名称”、“容器映像”和“资源组”文本框中输入以下值。 将其他值保留为默认值，然后单击“确定”。

* 容器名称：`mycontainer`
* 容器映像：`microsoft/aci-helloworld`
* 资源组：`myResourceGroup`

![在 Azure 门户中配置新的容器实例的基本设置][aci-portal-03]

可以在 Azure 容器实例中创建 Windows 和 Linux 容器。 在本快速入门教程中，我们将保留默认设置 Linux，由于我们在上一步中指定了基于 Linux 的容器 (`microsoft/aci-helloworld`)。

将“配置”中的其他设置保留为各自的默认设置，然后单击“确定”来验证配置。

![在 Azure 门户中配置新的容器实例][aci-portal-04]

完成验证后，将显示容器设置的摘要。 选择“确定”提交容器部署请求。

![Azure 门户中新的容器实例的设置摘要][aci-portal-05]

当启动部署时，将在门户仪表板上个放置一个磁贴，用于指示部署进度。 部署完成后，将更新该磁贴以显示新的 mycontainer myc1 容器组。

![Azure 门户中新的容器实例的创建进度][aci-portal-08]

选择 mycontainer myc1 容器组，以显示该容器组的属性。 记下该容器组的“IP 地址”，以及容器的“状态”。

![Azure 门户中的容器组概述][aci-portal-06]

容器进入“正在运行”状态后，请导航到你在上一步中记下的 IP 地址，以显示新容器中托管的应用程序。

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-portal-07]

## <a name="delete-the-container"></a>删除容器
处理完容器后，选择 **mycontainer-myc1** 容器组，然后单击“删除”。

![在 Azure 门户中删除容器实例][aci-portal-09]

这将启动确认对话框，出现提示时请选择“是”。

![Azure 门户中容器实例的删除确认][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
<!--[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png-->
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>后续步骤

在本快速入门中，从公共 Docker 中心存储库中的映像创建了 Azure 容器实例。 若要尝试使用 Azure 容器注册表自行生成容器并将其部署到 Azure 容器实例，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)