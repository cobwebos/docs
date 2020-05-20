---
title: （已弃用）Azure 容器服务快速入门 - 部署 DC/OS 群集
description: Azure 容器服务快速入门 - 部署 DC/OS 群集
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6274e24bae2e2a6eade0122fe244652eb29cacf9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399225"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>（已弃用）部署 DC/OS 群集

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS 提供了一个用于运行现代和容器化应用程序的分布式平台。 通过 Azure 容器服务，使预配生产就绪的 DC/OS 群集变得简单快捷。 本快速入门详细介绍了部署 DC/OS 群集和运行基本工作负荷所需的基本步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/reference-index#az-login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>创建 DC/OS 群集

使用 [az acs create](/cli/azure/acs#az-acs-create) 命令创建 DC/OS 群集。

下面的示例创建一个名为 myDCOSCluster  的 DC/OS 群集，并且在不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

在某些情况下（如限时试用），Azure 订阅对 Azure 资源的访问受限。 如果由于可用核心有限而导致部署失败，请将 `--agent-count 1` 添加到 [az acs creat](/cli/azure/acs#az-acs-create) 命令中，以减少默认代理计数。 

几分钟后，该命令完成并返回有关部署的信息。

## <a name="connect-to-dcos-cluster"></a>连接到 DC/OS 群集

创建 DC/OS 群集后，可以通过 SSH 隧道进行访问。 运行以下命令，以返回主 DC/OS 的公共 IP 地址。 该 IP 地址存储在一个变量中，并将在下一步使用。

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

要创建 SSH 隧道，请运行以下命令并按照屏幕上的说明进行操作。 如果正在使用端口 80，则该命令将失败。 将隧道端口更新为未在用的端口，例如 `85:localhost:80`。 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

可以通过浏览到 `http://localhost` 来测试 SSH 隧道。 如果 80 以外的端口已被使用，请调整位置以进行匹配。 

如果已成功创建 SSH 隧道，则返回 DC/OS 门户。

![DCOS UI](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>安装 DC/OS CLI

DC/OS 命令行接口用于从命令行管理 DC/OS 群集。 使用 [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli) 命令安装 DC/OS cli。 如果使用的是 Azure CloudShell，则 DC/OS CLI 已安装。 

如果在 macOS 或 Linux 上运行 Azure CLI，则可能需要将该命令与 sudo 一起运行。

```azurecli
az acs dcos install-cli
```

在 CLI 可用于群集之前，必须将它配置为使用 SSH 隧道。 为此，请运行以下命令，并根据需要调整端口。

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>运行应用程序

ACS DC/OS 群集的默认计划机制为 Marathon。 Marathon 用于启动应用程序和管理 DC/OS 群集上的应用程序状态。 若要通过 Marathon 计划应用程序，则请创建一个名为 marathon app.json 的文件  ，并将以下内容复制到其中。 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

运行以下命令以计划要在 DC/OS 群集上运行的应用程序。

```console
dcos marathon app add marathon-app.json
```

若要查看应用的部署状态，请运行以下命令。

```console
dcos marathon app list
```

当“WAITING”  列值从“True”  切换到“False”  时，应用程序部署已完成。

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

获取 DC/OS 群集代理的公共 IP 地址。

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

浏览到此地址将返回默认 NGINX 站点。

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>删除 DC/OS 群集

如果不再需要资源组、DC/OS 群集和所有相关的资源，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，你部署了一个 DC/OS 群集，并在群集上运行了一个简单的 Docker 容器。 若要详细了解 Azure 容器服务，请继续学习 ACS 教程。

> [!div class="nextstepaction"]
> [管理 ACS DC/OS 群集](container-service-dcos-manage-tutorial.md)
