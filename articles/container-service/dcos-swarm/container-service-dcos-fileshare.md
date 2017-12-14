---
title: "Azure DC/OS 群集的文件共享"
description: "在 Azure 容器服务中创建文件共享并将其装载到 DC/OS 群集"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>创建文件共享并将其装载到 DC/OS 群集

本教程详细介绍了如何在 Azure 中创建文件共享并将其装载到 DC/OS 群集的每个代理和主节点上。 设置文件共享可以更轻松地跨群集共享文件（如配置、访问权限和日志等）。 本教程中将完成以下任务：

> [!div class="checklist"]
> * 创建 Azure 存储帐户
> * 创建文件共享
> * 在 DC/OS 群集中装载共享

需要 ACS DC/OS 群集才能完成本教程中的步骤。 必要时，[此脚本示例](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)可为你创建一个。

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>在 Microsoft Azure 上创建文件共享

使用与 ACS DC/OS 群集共享 Azure 文件功能前，必须创建存储帐户和文件共享。 运行以下脚本来创建存储和文件共享。 使用环境中的内容更新参数。

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>在群集中装载共享

接下来，需要在群集内的各个虚拟机上装载文件共享。 此任务使用 cifs 工具/协议完成。 装载操作可在群集的各个节点上手动完成，也可以通过针对群集的各个节点运行脚本来完成。

本示例运行两个脚本，一个用于装载 Azure 文件共享，另一个用于在 DC/OS 群集的各个节点上运行此脚本。

首先需要 Azure 存储帐户名称和访问密钥。 运行以下命令获取此信息。 记录各个值，后续步骤中将使用这些值。

存储帐户名称：

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

存储帐户访问密钥：

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

接下来，获取 DC/OS 主节点的 FQDN，并将其存储在变量中。

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

将私钥复制到主节点。 需要使用此密钥与群集中的所有节点建立 ssh 连接。 如果创建群集时使用了非默认值，请更新用户名。 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

与基于 DC/OS 的集群的主节点（或第一个主节点）建立 SSH 连接。 如果创建群集时使用了非默认值，请更新用户名。

```azurecli-interactive
ssh azureuser@$FQDN
```

创建名为 cifsMount.sh 的文件，并将以下内容复制到其中。 

此脚本用于装载 Azure 文件共享。 使用前面收集的信息更新 `STORAGE_ACCT_NAME` 和 `ACCESS_KEY` 变量。

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
创建第二个文件，命名为 getNodesRunScript.sh，并将以下内容复制到该文件中。 

此脚本可发现所有群集节点，然后运行 cifsMount.sh 脚本，在各个节点上装载文件共享。

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

运行此脚本，在群集的所有节点上装载 Azure 文件共享。

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

现在可在群集各个节点上的 `/mnt/share/dcosshare` 处访问文件共享。

## <a name="next-steps"></a>后续步骤

在本教程中，使用以下步骤使 Azure 文件共享对 DC/OS 群集可用：

> [!div class="checklist"]
> * 创建 Azure 存储帐户
> * 创建文件共享
> * 在 DC/OS 群集中装载共享

请转到下一教程，了解如何将 Azure 容器注册表与 Azure 中的 DC/OS 集成。  

> [!div class="nextstepaction"]
> [使应用程序负载均衡](container-service-dcos-acr.md)
