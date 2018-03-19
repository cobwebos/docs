---
title: "快速入门 - 使用 Azure 门户在 Azure 中创建专用 Docker 注册表"
description: "快速了解如何使用 Azure 门户创建专用 Docker 容器注册表。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: db112f7f8f486093509a86f9781c30133925c25f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>使用 Azure 门户创建容器注册表

Azure 容器注册表是 Azure 中的专用 Docker 注册表，你可在其中存储和管理专用 Docker 容器映像。 在本快速入门中，请通过 Azure 门户创建一个容器注册表，将容器映像推送到注册表中，并最终将容器从注册表部署到 Azure 容器实例 (ACI) 中。

要完成本快速入门，必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure 门户 ( https://portal.azure.com )。

## <a name="create-a-container-registry"></a>创建容器注册表

选择“创建资源” > “容器” > “Azure 容器注册表”。

![在 Azure 门户中创建容器注册表][qs-portal-01]

在“注册表名称”和“资源组”中输入值。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 创建名为 `myResourceGroup` 的新资源组，对于 SKU 选择“Basic”。 选择“创建”，部署 ACR 实例。

![在 Azure 门户中创建容器注册表][qs-portal-03]

在本快速入门中，我们将创建基本注册表。 Azure 容器注册表以多个不同 SKU 提供，下表对此进行了简要说明。 有关每个 SKU 的更多详细信息，请参阅[容器注册表 SKU][container-registry-skus]。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

显示“部署成功”消息时，请在门户中选择容器注册表，然后选择“访问密钥”。

![在 Azure 门户中创建容器注册表][qs-portal-05]

在“管理员用户”下，选择“启用”。 记录以下值：

* 登录服务器
* 用户名
* password

使用 Docker CLI 处理注册表时，可在以下步骤中使用这些值。

![在 Azure 门户中创建容器注册表][qs-portal-06]

## <a name="log-in-to-acr"></a>登录到 ACR

在推送和拉取容器映像之前，必须登录到 ACR 实例。 请使用 [docker login][docker-login] 命令完成此操作。 使用在上一步中记录的值替换用户名、密码和登录服务器。

```bash
docker login --username <username> --password <password> <login server>
```

该命令在完成后返回 `Login Succeeded`。 可能会看见建议使用 `--password-stdin` 参数的安全警告。 虽然本文中未介绍它的用法，但我们建议按照此最佳做法进行操作。 有关详细信息，请参阅 [docker login][docker-login] 命令参考。

## <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表中，首先必须具有一个映像。 如果需要，请运行以下命令，从 Docker 中心拉取现有映像。

```bash
docker pull microsoft/aci-helloworld
```

将映像推送到注册表之前，必须使用 ACR 登录服务器名称标记映像。 使用 [docker tag][docker-tag] 命令标记映像。 使用之前记录的登录服务器名称替换登录服务器。

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

最后，使用 [docker push][docker-push] 将映像推送到 ACR 实例。 使用 ACR 实例的登录服务器名称替换登录服务器。

```bash
docker push <login server>/aci-helloworld:v1
```

成功的 `docker push` 命令的输出类似于：

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>列出容器映像

要列出 ACR 实例中的映像，请在门户中导航到注册表并选择“存储库”，然后选择使用 `docker push` 创建的存储库。

在本示例中，我们选择 aci-helloworld 存储库，并可在“标记”下看到 `v1` 标记的映像。

![在 Azure 门户中创建容器注册表][qs-portal-09]

## <a name="deploy-image-to-aci"></a>将映像部署到 ACI

若要从注册表部署到某个实例，需导航到存储库 (aci-helloworld)，然后单击 v1 旁边的省略号。

![从门户启动 Azure 容器实例][qs-portal-10]

显示上下文菜单后，请选择“运行实例”：

![启动 ACI 上下文菜单][qs-portal-11]

填写“容器名称”，确保选择正确的订阅，然后选择现有的**资源组**“myResourceGroup”，再单击“确定”以启动 Azure 容器实例。

![启动 ACI 部署选项][qs-portal-12]

启动部署时，会在门户仪表板上个放置一个磁贴，用于指示部署进度。 部署完成后，将更新该磁贴以显示新的 **mycontainer** 容器组。

![ACI 部署状态][qs-portal-13]

选择 mycontainer 容器组，以显示该容器组的属性。 请记下该容器组的“IP 地址”，以及容器的“状态”。

![ACI 容器详细信息][qs-portal-14]

## <a name="view-the-application"></a>查看应用程序

容器处于“正在运行”状态后，请通过喜欢的浏览器导航到在上一步中记下的 IP 地址，以显示应用程序。

![浏览器中的 Hello World 应用][qs-portal-15]

## <a name="clean-up-resources"></a>清理资源

若要清理资源，请导航到门户中的 **myResourceGroup** 资源组。 加载资源组以后，请单击“删除资源组”，以便删除资源组、Azure 容器注册表以及所有 Azure 容器实例。

![在 Azure 门户中创建容器注册表][qs-portal-08]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure CLI 创建 Azure 容器注册表，以及如何通过 Azure 容器实例启动该注册表的一个实例。 若要加深对 ACI 的了解，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
