---
title: 配置自定义 Linux 容器
description: 了解如何在 Azure 应用服务中配置自定义 Linux 容器。 本文介绍最常见的配置任务。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280139"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>为 Azure 应用服务配置自定义 Linux 容器

本文介绍如何配置自定义 Linux 容器以在 Azure 应用服务上运行。

本指南提供了应用服务中 Linux 应用容器化的关键概念和说明。 如果您从未使用过 Azure 应用服务，请先按照[自定义容器快速入门](quickstart-docker-go.md)和[教程](tutorial-custom-docker-image.md)操作。 还有一个[多容器应用程序快速启动](quickstart-multi-container.md)和[教程](tutorial-multi-container-app.md)。

## <a name="configure-port-number"></a>配置端口号

自定义映像中的 Web 服务器可能使用 80 以外的端口。 使用`WEBSITES_PORT`应用设置向 Azure 介绍自定义容器使用的端口。 [本教程中的 Python 示例](https://github.com/Azure-Samples/docker-django-webapp-linux)的 GitHub 页显示，需将 `WEBSITES_PORT` 设置为 _8000_。 您可以通过在云壳中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令来设置它。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>配置环境变量

自定义容器可能使用需要外部提供的环境变量。 您可以通过在云壳中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令来传递它们。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

此方法适用于单容器应用或多容器应用，其中环境变量在*docker-compose.yml*文件中指定。

## <a name="use-persistent-shared-storage"></a>使用持久共享存储

您可以使用应用文件系统中的 */home*目录在重新启动期间保留文件并在实例之间共享文件。 提供`/home`应用中的 ，以使容器应用能够访问持久存储。

禁用持久存储后，不会跨应用重新启动或`/home`跨多个实例持久化写入目录。 唯一的例外是`/home/LogFiles`目录，用于存储 Docker 和容器日志。 启用持久存储后，将保留对`/home`目录的所有写入，并可以由横向扩展应用的所有实例访问。

默认情况下，*已启用*持久存储，并且该设置不会在"应用程序设置"中公开。 要禁用它，请通过在`WEBSITES_ENABLE_APP_SERVICE_STORAGE`云外壳中运行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令来设置应用设置。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 您还可以[配置自己的持久存储](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>启用 SSH

SSH 实现容器和客户端之间的安全通信。 为了使自定义容器支持 SSH，必须将其添加到 Dockerfile 本身。

> [!TIP]
> 所有内置 Linux 容器都在其映像存储库中添加了 SSH 指令。 您可以使用[Node.js 10.14 存储库](https://github.com/Azure-App-Service/node/blob/master/10.14)执行以下说明，了解该存储库的启用方式。

- 使用[RUN](https://docs.docker.com/engine/reference/builder/#run)指令安装 SSH 服务器并将根帐户的密码设置为`"Docker!"`。 例如，对于基于[阿尔卑斯 Linux](https://hub.docker.com/_/alpine)的图像，您需要以下命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此配置不允许与容器的外部连接。 SSH 只能通过发布凭据`https://<app-name>.scm.azurewebsites.net`进行身份验证。

- [将此sshd_config文件](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)添加到映像存储库，并使用[COPY](https://docs.docker.com/engine/reference/builder/#copy)指令将该文件复制到 */etc/ssh/* 目录。 有关*sshd_config*文件的详细信息，请参阅[OpenBSD 文档](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 文件必须包括以下项**：
    > - `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

- 使用[EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)指令在容器中打开端口 2222。 虽然根密码已知，但端口 2222 可从 Internet 访问。 它只能由专用虚拟网络桥网中的容器访问。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器的启动脚本中，启动 SSH 服务器。

    ```bash
    /usr/sbin/sshd
    ```

    例如，请参阅默认[Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)如何启动 SSH 服务器。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>配置多容器应用

- [在 Docker 撰写中使用持久存储](#use-persistent-storage-in-docker-compose)
- [预览版限制](#preview-limitations)
- [Docker 组合选项](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker 撰写中使用持久存储

WordPress 等多容器应用需要持久存储才能正常运行。 要启用它，Docker 合成配置必须指向容器*外部*的存储位置。 容器内的存储位置不会保留超出应用重新启动的更改。

使用云壳中的 az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Webapp 配置应用设置集](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令设置应用设置，启用持久存储。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在*docker-compose.yml*文件中，将`volumes`选项映射到`${WEBAPP_STORAGE_HOME}`。 

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

多容器当前处于预览状态。 不支持以下应用服务平台功能：

- 身份验证/授权
- 托管标识

### <a name="docker-compose-options"></a>Docker 组合选项

以下列表显示受支持且不支持的 Docker 组合配置选项：

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
> 在"公共预览"中，将忽略未明确调用的任何其他选项。

## <a name="configure-vnet-integration"></a>配置 VNet 集成

使用具有 VNet 集成的自定义容器可能需要其他容器配置。 请参阅[将应用与 Azure 虚拟网络集成](../web-sites-integrate-with-vnet.md)。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：从专用容器存储库进行部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用程序](tutorial-multi-container-app.md)
