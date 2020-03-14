---
title: 配置自定义 Linux 容器
description: 了解如何在 Azure App Service 中配置自定义 Linux 容器。 本文介绍最常见的配置任务。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280139"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>为 Azure App Service 配置自定义 Linux 容器

本文介绍如何配置自定义 Linux 容器，使其在 Azure App Service 上运行。

本指南提供有关应用服务中 Linux 应用的容器化的重要概念和说明。 如果你从未使用过 Azure App Service，请先按照[自定义容器快速入门](quickstart-docker-go.md)和[教程](tutorial-custom-docker-image.md)操作。 还有[多容器应用快速入门](quickstart-multi-container.md)和[教程](tutorial-multi-container-app.md)。

## <a name="configure-port-number"></a>配置端口号

自定义映像中的 web 服务器可能使用80以外的端口。 通过使用 "`WEBSITES_PORT` 应用" 设置，通知 Azure 有关自定义容器使用的端口。 [本教程中的 Python 示例](https://github.com/Azure-Samples/docker-django-webapp-linux)的 GitHub 页显示，需将 `WEBSITES_PORT` 设置为 _8000_。 可以通过在 Cloud Shell 中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令对其进行设置。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>配置环境变量

自定义容器可能会使用需要在外部提供的环境变量。 可以通过在 Cloud Shell 中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令来传入它们。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

此方法适用于单容器应用或多容器应用，其中环境变量是在*docker-compose.yml. docker-compose.override.yml*文件中指定的。

## <a name="use-persistent-shared-storage"></a>使用永久共享存储

你可以使用应用文件系统中的 */home*目录跨重启保存文件并在实例之间共享这些文件。 提供应用中的 `/home`，使容器应用能够访问永久性存储。

禁用永久性存储后，不会在应用重启或多个实例之间持续写入 `/home` 目录。 唯一的例外是用于存储 Docker 和容器日志的 `/home/LogFiles` 目录。 启用永久性存储时，所有写入 `/home` 目录的操作都将被保留，并且可由向外扩展的应用程序的所有实例访问。

默认情况下，将*启用*持久性存储，并且不会在应用程序设置中公开设置。 若要禁用此设置，请在 Cloud Shell 中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令，设置 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 应用程序设置。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 你还可以[配置自己的持久存储](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>启用 SSH

SSH 实现容器和客户端之间的安全通信。 为了使自定义容器支持 SSH，你必须将其添加到 Dockerfile 本身。

> [!TIP]
> 所有内置 Linux 容器都已将 SSH 说明添加到了其映像存储库中。 你可以通过 node.js [10.14 存储库](https://github.com/Azure-App-Service/node/blob/master/10.14)完成以下说明，以查看其在此中的启用方式。

- 使用[运行](https://docs.docker.com/engine/reference/builder/#run)指令安装 SSH 服务器，并将根帐户的密码设置为 `"Docker!"`。 例如，对于基于[Alpine Linux](https://hub.docker.com/_/alpine)的映像，需要以下命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此配置不允许外部连接到容器。 SSH 仅通过 `https://<app-name>.scm.azurewebsites.net` 提供，并使用发布凭据进行身份验证。

- 将[此 sshd_config 文件](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)添加到映像存储库，并使用[copy](https://docs.docker.com/engine/reference/builder/#copy)指令将文件复制到 */etc/ssh/* 目录中。 有关*sshd_config*文件的详细信息，请参阅[OpenBSD 文档](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 文件必须包括以下项：
    > - `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

- 使用[公开](https://docs.docker.com/engine/reference/builder/#expose)说明在容器中打开端口2222。 尽管根密码已知，但无法从 internet 访问端口2222。 它只能由专用虚拟网络的桥网络中的容器访问。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器的启动脚本中启动 SSH 服务器。

    ```bash
    /usr/sbin/sshd
    ```

    有关示例，请参阅默认[node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)如何启动 SSH 服务器。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>配置多容器应用

- [在 Docker Compose 中使用持久性存储](#use-persistent-storage-in-docker-compose)
- [预览限制](#preview-limitations)
- [Docker Compose 选项](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 中使用持久性存储

多容器应用（如 WordPress）需要持久存储才能正常工作。 若要启用它，你的 Docker Compose 配置必须指向容器*外部*的存储位置。 在应用重新启动后，容器中的存储位置不会保存更改。

使用 Cloud Shell 中的[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令设置 "`WEBSITES_ENABLE_APP_SERVICE_STORAGE`" 应用设置，启用持久存储。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在*docker-compose.override.yml*文件中，将 `volumes` 选项映射到 "`${WEBAPP_STORAGE_HOME}`"。 

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

多容器目前处于预览阶段。 不支持以下应用服务平台功能：

- 身份验证/授权
- 托管标识

### <a name="docker-compose-options"></a>Docker Compose 选项

以下列表显示了支持的和不支持的 Docker Compose 配置选项：

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
> 公共预览版中将忽略未显式调用的任何其他选项。

## <a name="configure-vnet-integration"></a>配置 VNet 集成

将自定义容器与 VNet 集成结合使用时，可能需要额外的容器配置。 请参阅[将应用与 Azure 虚拟网络集成](../web-sites-integrate-with-vnet.md)。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：从专用容器存储库进行部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)
