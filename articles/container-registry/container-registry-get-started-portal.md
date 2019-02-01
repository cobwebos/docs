---
title: 快速入门 - 在 Azure 中创建专用 Docker 注册表 - Azure 门户
description: 快速了解如何使用 Azure 门户创建专用 Docker 容器注册表。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 93c22475a4043d1cbf5cb0ad7f9b134e8ac717cc
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298409"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建专用容器注册表

Azure 容器注册表是 Azure 中的专用 Docker 注册表，你可在其中存储和管理专用 Docker 容器映像。 在本快速入门教程中，你会使用 Azure 门户创建容器注册表。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

若要登录到注册表以使用容器映像，本快速入门要求运行 Azure CLI（建议使用 2.0.55 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-container-registry"></a>创建容器注册表

选择“创建资源” > “容器” > “容器注册表”。

![在 Azure 门户中创建容器注册表][qs-portal-01]

在“注册表名称”和“资源组”中输入值。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 对于本快速入门，在 `West US` 位置创建名为 `myResourceGroup` 的新资源组，对于 **SKU**，选择“基本”。 选择“创建”，部署 ACR 实例。

![在 Azure 门户中创建容器注册表][qs-portal-03]

本快速入门将创建一个“基本”注册表。该注册表已针对成本进行优化，是可供开发人员了解 Azure 容器注册表的选项。 有关可用服务层的详细信息，请参阅[容器注册表 SKU][container-registry-skus]。

显示“部署成功”消息时，请在门户中选择容器注册表。 

![Azure 门户中的容器注册表概述][qs-portal-05]

记下“登录服务器”的值。 结合 Azure CLI 和 Docker 使用注册表时，请在以下步骤中使用此值。

## <a name="log-in-to-registry"></a>登录到注册表

在推送和拉取容器映像之前，必须登录到 ACR 实例。 在操作系统中打开命令外壳，然后在 Azure CLI 中使用 [az acr login][az-acr-login] 命令。

```azurecli
az acr login --name <acrName>
```

该命令在完成后返回 `Login Succeeded`。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

若要列出注册表中的映像，请在门户中导航到注册表并选择“存储库”，然后选择使用 `docker push` 创建的存储库。

在本示例中，我们选择了 **busybox** 存储库，然后可在“标记”下看到 `v1` 标记的映像。

![在 Azure 门户中列出容器映像][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

若要清理资源，请在门户中导航到 **myResourceGroup** 资源组。 加载该资源组后，单击“删除资源组”以删除该资源组、容器注册表以及其中存储的容器映像。

![在 Azure 门户中删除资源组][qs-portal-08]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 门户创建 Azure 容器注册表、推送容器映像，以及提取和运行注册表中的映像。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
