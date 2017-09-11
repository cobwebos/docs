---
title: "在 Azure 中的 Linux VM 上使用 Docker Compose | Microsoft Docs"
description: "如何通过 Azure CLI 在 Linux 虚拟机上使用 Docker 和 Compose"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 541722cb02dd991228726e62a2304b49cdd806f2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>使用 Docker 和 Compose 在 Azure 中定义和运行多容器应用程序入门
借助 [Compose](http://github.com/docker/compose)，可以使用简单的文本文件定义由多个 Docker 容器组成的应用程序。 然后使用单个命令启动应用程序，该命令会执行部署定义的环境所需的所有操作。 作为示例，本文说明如何在 Ubuntu VM 上使用后端 MariaDB SQL 数据库快速设置 WordPress 博客。 也可以使用 Compose 设置更复杂的应用程序。


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>将 Linux VM 设置为 Docker 主机
可以使用各种 Azure 过程和 Azure Marketplace 中提供的映像或 Resource Manager 模板创建 Linux VM，并将其设置为 Docker 主机。 例如，请参阅[使用 Docker VM 扩展部署环境](dockerextension.md)，了解使用[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)通过 Azure Docker VM 扩展快速创建 Ubuntu VM。 

使用 Docker VM 扩展时，VM 会自动设置为 Docker 主机，并且已安装 Compose。


### <a name="create-docker-host-with-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Docker 主机
安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

首先，使用 [az group create](/cli/azure/group#create) 为 Docker 环境创建资源组。 以下示例在 westus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

接下来，使用 [az group deployment create](/cli/azure/group/deployment#create) 部署 VM，其中包含 [GitHub 中此 Azure Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)中的 Azure Docker VM 扩展。 为 newStorageAccountName、adminUsername、adminPassword 和 dnsNameForPublicIP 提供自己的值：

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

需要几分钟才能完成部署。 部署完成后，[移到下一步](#verify-that-compose-is-installed)，将 SSH 移到 VM。 

（可选）要改为将控制返回提示符，并允许部署在后台继续运行，请将 `--no-wait` 标志添加到前一个命令。 此过程允许在 CLI 中执行其他工作，同时部署将持续几分钟。 可以使用 [az vm show](/cli/azure/vm#show) 查看有关 Docker 主机状态的详细信息。 以下示例在名为 myResourceGroup 的资源组中检查名为 myDockerVM（模板中的默认名称 - 请不要更改该名称）的 VM 的状态：

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

当此命令返回“已成功”时，表示部署完毕，可使用以下步骤将 SSH 移到 VM。


## <a name="verify-that-compose-is-installed"></a>确认已安装 Compose
部署完成之后，使用在部署期间提供的 DNS 名称通过 SSH 连接到新 Docker 主机。 可使用 `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` 查看 VM 的详细信息，包括 DNS 名称。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

若要检查 VM 上是否安装了 Compose，请运行以下命令：

```bash
docker-compose --version
```

将看到类似于 docker-compose 1.6.2, build 4d72027 的输出。

> [!TIP]
> 如果使用另一种方法创建 Docker 主机，而且需要自行安装 Compose，请参阅 [Compose 文档](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)。


## <a name="create-a-docker-composeyml-configuration-file"></a>创建 docker-compose.yml 配置文件
接下来，将创建 `docker-compose.yml` 文件，它只是一个文本配置文件，用于定义要在 VM 上运行的 Docker 容器。 该文件指定要在每个容器中运行的映像（或者它可能从 Dockerfile 生成）、必要的环境变量和依赖关系、端口以及容器之间的链接。 有关 yml 文件语法的详细信息，请参阅 [Compose 文件参考](https://docs.docker.com/compose/compose-file/)。

创建 docker-compose.yml 文件，如下所示：

```bash
touch docker-compose.yml
```

使用最喜欢的文本编辑器将一些数据添加到文件。 以下示例使用 vi 编辑器：

```bash
vi docker-compose.yml
```

将以下示例粘贴到文本文件中。 此配置将使用 [DockerHub 注册表](https://registry.hub.docker.com/_/wordpress/)中的映像安装 WordPress（开源博客和内容管理系统）和链接的后端 MariaDB SQL 数据库。 输入自己的 MYSQL_ROOT_PASSWORD，如下所示：

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>使用 Compose 启动容器
在 docker-compose.yml 文件所在的目录中，运行以下命令（可能需要使用 `sudo` 运行 `docker-compose`，具体取决于用户环境）：

```bash
docker-compose up -d
```

此命令启动 docker-compose.yml 中指定的 Docker 容器。 完成此步骤需要一两分钟时间。 将显示类似于以下示例的输出：

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> 启动时请务必使用 **-d** 选项，使容器在后台继续运行。


若要验证容器是否已启动，请键入 `docker-compose ps`。 应看到类似如下的内容：

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

现在可以在 VM 的端口 80 上直接连接到 WordPress。 打开 Web 浏览器并输入 VM 的 DNS 名称（如 `http://mypublicdns.westus.cloudapp.azure.com`）。 现在，应看到 WordPress 开始屏幕，可以在其中完成安装并开始使用应用程序。

![WordPress 开始屏幕][wordpress_start]

## <a name="next-steps"></a>后续步骤
* 转到 [Docker VM 扩展用户指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)，了解用于在 Docker VM 中配置 Docker 和 Compose 的更多选项。 例如，其中一个选项是将 Compose yml 文件（已转换为 JSON）直接放在 Docker VM 扩展的配置中。
* 有关构建和部署多容器应用的更多示例，请查阅 [Compose 命令行参考](http://docs.docker.com/compose/reference/)和[用户指南](http://docs.docker.com/compose/)。
* 使用 Azure Resource Manager 模板（自己的或[社区](https://azure.microsoft.com/documentation/templates/)提供的），通过 Docker 部署 Azure VM 和使用 Compose 设置的应用程序。 例如，[使用 Docker 部署 WordPress 博客](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql)模板使用 Docker 和 Compose 通过 Ubuntu VM 上的 MySQL 后端快速部署 WordPress。
* 请尝试将 Docker Compose 与 Docker Swarm 群集集成。 相关方案请参阅 [Using Compose with Swarm](https://docs.docker.com/compose/swarm/)（将 Compose 与 Swarm 配合使用）。

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png

