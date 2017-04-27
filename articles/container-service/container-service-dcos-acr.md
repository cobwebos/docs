---
title: "将 ACR 与 Azure DC/OS 群集配合使用 | Microsoft Docs"
description: "在 Azure 容器服务中将 Azure 容器注册表与 DC/OS 群集配合使用"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, 容器, 微服务, Mesos, Azure, 文件共享, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>将 ACR 与 DC/OS 群集配合使用，部署应用程序

在本文中，将探讨如何将 ACR（Azure 容器注册表）等专用容器注册与 DC/OS 群集配合使用。 利用 ACR 可私下存储映像并保持对其控制，例如版本和/或示例更新。

完成此示例之前，需要： 
* 在 Azure 容器服务中配置的 DC/OS 群集。 请参阅[部署 Azure 容器服务群集](container-service-deployment.md)。
* 已部署的 Azure 容器服务。 请参阅[使用 Azure 门户创建私有 Docker 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)或[使用 Azure CLI 2.0 创建专用 Docker 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
* DC/OS 群集内配置的文件共享。 请参阅[创建文件共享并将其装载到 DC/OS 群集](container-service-dcos-fileshare.md)
* 了解如何使用 [Web UI](container-service-mesos-marathon-ui.md) 或 [REST API](container-service-mesos-marathon-rest.md) 部署 DC/OS 群集中的 Docker 映像

## <a name="manage-the-authentication-inside-your-cluster"></a>管理群集内的身份验证

推送映像和从专用注册表请求映像的传统方法是，首先对其进行身份验证。 若要执行此操作，必须在任何需要使用专用注册表的 docker 客户端过程中使用 `docker login` 命令行。
进入生产环境后，使用 DC/OS（本例中），要确保能够从任何节点请求映像。 这意味着要自动执行身份验证过程，并不在每台计算机上运行命令行，因为根据群集大小，可以预想到其中可能存在问题和大量操作。 

假设已[在 DC/OS 内设置文件共享](container-service-dcos-fileshare.md)，将通过以下操作进行使用：

### <a name="from-any-client-machine-recommended-method"></a>从任何客户端计算机 [推荐方法]

以下命令可在任何环境 (Windows/Mac/Linux) 上运行：

1. 请确保满足以下先决条件：
  * TAR 工具
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [其他](https://www.docker.com/get-docker)
  * [通过以下方法](container-service-dcos-fileshare.md)在群集内装载文件共享

2. 通过对偏爱的终端执行以下命令，启动对 ACR 服务的身份验证：`sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`。 必须将 `USERNAME`、`PASSWORD` 和 `ACR-REGISTRY-NAME` 变量替换为 Azure 门户中提供的值

3. 值得关注的是正在执行 `docker login` 操作时，这些值存储在本地计算机的主文件夹下（Mac 和 Linux 上的 `cd ~/.docker` 或 Windows 上的 `cd %HOMEPATH%`）。 我们将使用 `tar czf` 命令压缩此文件夹中的内容。

4. 最后一步是复制[已根据先决条件创建](container-service-dcos-fileshare.md)的文件共享中刚刚创建的 tar 文件。 这可以通过将 Azure CLI 与以下命令 `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>` 配合使用实现

最后，此处是一个使用以下设置（使用 windows 环境）的示例：
* ACR 名称：**`demodcos`**
* 用户名：**`demodcos`**
* 密码：**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 存储帐户名称：**`anystorageaccountname`**
* 存储帐户密钥：**`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* 在存储帐户内创建的共享名称：**`share`**
* 要上传的 tar 存档的完整路径：**`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>从主机 [不推荐的方法]

不推荐从主机执行操作，以避免错误和对整个环境的影响。

1. 首先，将 SSH 连接到基于 DC/OS 的集群的主节点（或第一个主节点）。 例如 `ssh userName@masterFQDN –A –p 22`，其中 masterFQDN 是主 VM 的完全限定的域名。 [单击此处查看详细信息](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. 通过执行以下命令，启动对 ACR 服务的身份验证：`sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`。 必须将 `USERNAME`、`PASSWORD` 和 `ACR-REGISTRY-NAME` 变量替换为 Azure 门户中提供的值

3. 值得关注的是正在执行 `docker login` 操作时，这些值存储在本地计算机的主文件夹 `~/.docker` 下。 我们将使用 `tar czf` 命令压缩此文件夹中的内容。

4. 最后一步是复制文件共享中刚刚创建的 tar 文件。 此操作将允许在群集内所有的虚拟机中使用此凭据，并在 Azure 容器注册表中进行身份验证。

最后，此处是一个使用以下设置的示例：
* ACR 名称：**`demodcos`**
* 用户名：**`demodcos`**
* 密码：**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 群集内的装入点：**`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>从具有 Marathon 的 ACR 部署映像

据推测，已推送要在容器注册表内部署的映像。 请参阅[使用 Docker CLI 将第一个映像推送到专用 Docker 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

假设要使用 **2.1** 标记从托管在 Azure (ACR) 上的专用注册表部署 **simple-web** 映像，将使用以下配置：

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> 可以看到我们将使用 **uris** 选项指定凭据的存储位置。
>

## <a name="next-steps"></a>后续步骤
* 阅读有关[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)的详细信息。
* 通过 [Marathon REST API](container-service-mesos-marathon-rest.md) 管理 DC/OS 容器。
