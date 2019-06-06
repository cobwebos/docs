---
title: 配置自定义容器的 Azure 应用服务 |Microsoft Docs
description: 了解如何配置要在 Azure 应用服务中运行的 Node.js 应用程序
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 9bfd6c34d81e193fe31610f840474f1e4c91170d
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430924"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>为 Azure 应用服务配置自定义 Linux 容器

本文介绍如何配置 Azure 应用服务上运行的自定义 Linux 容器。

本指南提供关键概念和有关应用服务中的 Linux 应用的容器化的说明。 如果你从未使用过 Azure 应用服务，请按照[自定义容器快速入门](quickstart-docker-go.md)并[教程](tutorial-custom-docker-image.md)第一个。 此外，还有[多容器应用快速入门](quickstart-multi-container.md)并[教程](tutorial-multi-container-app.md)。

## <a name="configure-port-number"></a>配置端口号

自定义映像中的 web 服务器可能使用非 80 的端口。 通过使用自定义容器的端口告知 Azure`WEBSITES_PORT`应用设置。 [本教程中的 Python 示例](https://github.com/Azure-Samples/docker-django-webapp-linux)的 GitHub 页显示，需将 `WEBSITES_PORT` 设置为 _8000_。 可以将其设置通过运行[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>配置环境变量

自定义容器可能会使用需要从外部提供的环境变量。 您可以通过运行传递中对其[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

此方法适用于单容器应用程序或环境变量中的指定其中的多容器应用的同时*docker compose.yml*文件。

## <a name="use-persistent-shared-storage"></a>使用持久的共享的存储

可以使用 */home*目录中应用的文件系统在重新启动时持久保存文件并在实例之间共享它们。 `/home`应用程序中用于启用容器应用程序需访问持久性存储。

永久性存储时处于禁用状态，然后将写入`/home`目录不会持久保存在应用重启后或跨多个实例。 唯一的例外是`/home/LogFiles`目录，用于存储 Docker 和容器日志。 启用持久存储时，所有写入到`/home`目录会保留，并且可以通过向外扩展应用的所有实例访问。

持久存储是默认情况下*禁用*。 若要启用或禁用它，请设置`WEBSITES_ENABLE_APP_SERVICE_STORAGE`通过运行的应用设置[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> 此外可以[配置持久存储](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>启用 SSH

SSH 实现容器和客户端之间的安全通信。 为了使自定义容器，以支持 SSH，必须将其添加到 Dockerfile 本身。

> [!TIP]
> 所有内置的 Linux 容器在其映像存储库中添加了 SSH 说明。 您可通过使用下面的说明完成[存储库 Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14)若要查看如何启用它存在。

- 使用[运行](https://docs.docker.com/engine/reference/builder/#run)指令来安装 SSH 服务器和设置到的根帐户密码`"Docker!"`。 例如，对于基于映像[Alpine Linux](https://hub.docker.com/_/alpine)，需要以下命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此配置不允许对容器的外部连接。 SSH 是只能通过`https://<app-name>.scm.azurewebsites.net`和使用发布凭据进行身份验证。

- 添加[此 sshd_config 文件](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)到映像存储库，并使用[副本](https://docs.docker.com/engine/reference/builder/#copy)指令，以将文件复制到 */etc/ssh/* 目录。 有关详细信息*sshd_config*文件，请参阅[OpenBSD 文档](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 文件必须包括以下项  ：
    > - `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

- 使用[公开](https://docs.docker.com/engine/reference/builder/#expose)指令，以在容器中打开端口 2222年。 虽然根密码已知，是无法从 internet 访问端口 2222年。 它是可访问仅由专用的虚拟网络的桥网络中的容器。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器启动脚本中，启动 SSH 服务器。

    ```bash
    /usr/sbin/sshd
    ```

    有关示例，请参阅如何默认[Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)启动 SSH 服务器。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>配置多容器应用

- [使用永久存储在 Docker Compose](#use-persistent-storage-in-docker-compose)
- [预览版限制](#preview-limitations)
- [Docker Compose 选项](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>使用永久存储在 Docker Compose

WordPress 等的多容器应用程序需要持久性存储区才能正常工作。 若要启用它，你的 Docker Compose 配置必须指向存储位置*外部*容器。 在容器的存储位置不会保留更改超出应用重启。

通过设置启用持久存储`WEBSITES_ENABLE_APP_SERVICE_STORAGE`应用设置，请使用[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令，在 Cloud Shell。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在你*docker-compose.yml*文件中，映射`volumes`选项设为`${WEBAPP_STORAGE_HOME}`。 

`WEBAPP_STORAGE_HOME` 是应用服务中已映射到应用持续性存储的环境变量。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>预览版限制

多容器目前处于预览状态。 不支持以下应用服务平台功能：

- 身份验证/授权
- 托管标识

### <a name="docker-compose-options"></a>Docker Compose 选项

以下列表显示了支持和不支持 Docker Compose 配置选项：

#### <a name="supported-options"></a>支持的选项

- command
- entrypoint
- 环境
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>不支持的选项

- build（不允许）
- depends_on（忽略）
- networks（忽略）
- secrets（忽略）
- 除了 80 和 8080 之外的端口（忽略）

> [!NOTE]
> 在公共预览版中，将忽略不显式调出的任何其他选项。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：从专用容器存储库进行部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)
