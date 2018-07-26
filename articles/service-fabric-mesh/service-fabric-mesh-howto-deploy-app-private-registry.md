---
title: 将应用从专用注册表部署到 Azure Service Fabric 网格 | Microsoft Docs
description: 了解如何将使用专用容器注册表的应用部署到使用 Azure CLI 的 Service Fabric 网格。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089480"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>从专用容器映像注册表部署 Service Fabric 网格应用

本文介绍如何部署使用专用容器映像注册表的 Azure Service Fabric 网格应用。

## <a name="prerequisites"></a>先决条件

### <a name="set-up-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI 
可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成此任务。 根据这些[说明](service-fabric-mesh-howto-setup-cli.md)安装 Azure Service Fabric 网格 CLI 扩展模块。

### <a name="install-docker"></a>安装 Docker

安装 Docker，用于支持 Service Fabric 网格使用的容器化 Service Fabric 应用。

### <a name="windows-10"></a>Windows 10

下载并安装最新版本的 [Docker Community Edition for Windows][download-docker]。 

在安装过程中出现提示时，请选择“使用 Windows 容器而不是 Linux 容器”。 需要注销然后重新登录。 重新登录后，如果以前未启用 Hyper-V，系统可能会提示你启用 Hyper-V。 启用 Hyper-V，然后重启计算机。

重启计算机后，Docker 会提示你启用“容器”功能。请启用该功能，然后重启计算机。

### <a name="windows-server-2016"></a>Windows Server 2016

使用以下 PowerShell 命令安装 Docker。 有关详细信息，请参阅 [Docker Enterprise Edition for Windows Server][download-docker-server]。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

重启计算机。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 并设置活动订阅：

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>创建容器注册表并向其推送映像

按照[使用 Azure CLI 在 Azure 中创建专用 Docker 注册表](../container-registry/container-registry-get-started-azure-cli.md)中的说明创建 Azure 容器注册表。 执行[登录 ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) 步骤之前的步骤。 

### <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如果尚不具有任何本地容器映像，请运行以下命令，从 Docker 中心拉取现有映像。

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

将映像推送到注册表之前，必须使用 ACR 登录服务器的完全限定的名称进行标记。 运行以下命令，获取 ACR 实例的完整登录服务器名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag][docker-tag] 命令标记映像。 使用 ACR 实例的登录服务器名称替换 `<acrLoginServer>`。

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>列出容器映像

以下示例列出了注册表中的存储库：

```azurecli
az acr repository list --name <acrName> --output table
```

输出：

```bash
Result
----------------
azure-mesh-helloworld
```

以下示例列出了 azure-mesh-helloworld 存储库中的标记。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

输出：

```bash
Result
--------
1.1-alpine
```
上面的输出确认专用容器注册表中存在 `azure-mesh-helloworld:1.1-alpine`。

## <a name="retrieve-credentials-for-the-registry"></a>检索注册表凭据

要从已创建注册表中部署容器实例，必须在部署期间提供凭据。 使用以下命令对注册表启用管理员用户：

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

使用以下命令获取注册表服务器名称、用户名和密码：

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

前面的命令提供的值在以下命令中引用为 `<acrLoginServer>`、`<acrUserName>` 和 `<acrPassword>`。


## <a name="deploy-the-template"></a>部署模板

使用以下命令创建应用程序和相关资源，并提供上一步中的凭据。

模板中的 `registry-password` 参数是 `securestring`。 它不会显示在部署状态和 `az mesh service show` 命令中。 请确保它在以下命令中正确指定。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

几分钟后，命令应返回：

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>打开应用程序
在应用程序成功部署后，获取服务终结点的公用 IP 地址并在浏览器中打开。 它显示包含 Service Fabric 网格徽标的网页。

部署命令将返回服务终结点的公共 IP 地址。 （可选）还可以通过查询网络资源来查找服务终结点的公共 IP 地址。 
 
此应用程序的网络资源名称是 `helloWorldPrivateRegistryNetwork`，使用以下命令提取与其相关的信息。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>删除资源

为节省为预览计划分配的有限资源，请定期删除资源。 要删除与此示例相关的资源，请删除在其中部署的资源组。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>后续步骤
- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) 上查看 Hello World 应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/