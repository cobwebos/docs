---
title: "Azure DC/OS 群集的文件共享 | Microsoft Docs"
description: "在 Azure 容器服务中创建文件共享并将其装载到 DC/OS 群集"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure, 文件共享, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>创建文件共享并将其装载到 DC/OS 群集
本文介绍如何在 Azure 上创建文件共享并将其装载到 DC/OS 群集的每个代理和主节点上。 设置文件共享可以更轻松地跨群集共享文件（如配置、访问权限和日志等）。

开始了解此示例前，需要一个在 Azure 容器服务中配置的 DC/OS 群集。 请参阅[部署 Azure 容器服务群集](container-service-deployment.md)。

## <a name="create-a-file-share-on-microsoft-azure"></a>在 Microsoft Azure 上创建文件共享
### <a name="using-the-portal"></a>使用门户

1. 登录门户。
2. 创建存储帐户。
   
  ![Azure 容器服务创建存储帐户](media/container-service-dcos-fileshare/createSA.png)

3. 创建后，请在“服务”部分中单击“文件”。
   
  ![Azure 容器服务“文件”部分](media/container-service-dcos-fileshare/filesServices.png)

4. 单击“+ 文件共享”，并输入此新共享的名称（不必设置“配额”）。
   
  ![Azure 容器服务“+ 文件共享”](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>使用 Azure CLI 2.0

如果需要，请[安装和设置 Azure CLI](/cli/azure/install-azure-cli.md)。

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>在群集中装载共享

接下来，需要使用 cifs 工具/协议在群集内的每个虚拟机上装载此共享。 使用以下命令行执行该操作：`mount -t cifs`。

以下是一个示例，其中使用了：
* 存储帐户名称 **`anystorageaccountname`**
* 虚构帐户密钥 **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* 装入点 **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

将在群集（主节点和代理节点）的每个虚拟机上运行此命令。 如果代理数量很大，建议通过创建脚本自动执行此过程。  

### <a name="set-up-scripts"></a>设置脚本

1. 首先，将 SSH 连接到基于 DC/OS 的集群的主节点（或第一个主节点）。 例如 `ssh userName@masterFQDN –A –p 22`，其中 masterFQDN 是主 VM 的完全限定的域名。

2. 将私钥复制到主节点上的工作目录 (~) 中。

3. 使用以下命令更改其上的权限：`chmod 600 yourPrivateKeyFile`。

4. 使用 `ssh-add yourPrivateKeyFile` 命令导入私钥。 如果第一次操作无效，则可能需要运行 `eval ssh-agent -s`。

5. 使用你喜爱的编辑器（如 vi、nano 或 vim）从主节点中创建两个文件： 
  
  * 其中一个名为 **cifsMount.sh**，具有要在每个 VM 上执行的脚本 
  * 另一个名为 **mountShares.sh**，用于启动将调用第一个脚本的所有 SSH 连接


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> 必须使用自己的设置（如存储帐户的名称和密码）来更改 **'mount'** 命令。
>  

之前创建脚本的文件夹中现在应有 3 个文件：  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>运行脚本

使用以下命令执行 **mountShares.sh** 文件：`sh mountShares.sh`。

应当会看到结果出现在终端中。 脚本执行完成后，即可在群集中使用文件共享。

可以优化脚本，但本示例非常简单，其目的是提供指导。

> [!NOTE] 
> 对于要求高 IOPS 的方案，不建议使用此方法，但是该方法在群集之间共享文档和信息会非常有用。
>

## <a name="next-steps"></a>后续步骤
* 阅读有关[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)的详细信息。
* 通过 [Marathon REST API](container-service-mesos-marathon-rest.md) 管理 DC/OS 容器。
