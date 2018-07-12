---
title: 使用 Azure CLI 在 Linux VM 上安装 MongoDB | Microsoft Docs
description: 了解如何使用 Azure CLI 2.0 在 Linux 虚拟机上安装和配置 MongoDB
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d066b412fb722318824a408861fe7d9595c71537
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928227"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>如何在 Linux VM 上安装和配置 MongoDB
[MongoDB](http://www.mongodb.org) 是一个流行的开源、高性能 NoSQL 数据库。 本文介绍如何使用 Azure CLI 2.0 在 Linux VM 上安装和配置 MongoDB。 文中提供了一些示例，详细说明如何执行以下操作：

* [手动安装和配置基本 MongoDB 实例](#manually-install-and-configure-mongodb-on-a-vm)
* [使用 Resource Manager 模板创建基本 MongoDB 实例](#create-basic-mongodb-instance-on-centos-using-a-template)
* [使用 Resource Manager 模板创建包含副本集的复杂 MongoDB 分片群集](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>在 VM 上手动安装和配置 MongoDB
MongoDB 为 Red Hat/CentOS、SUSE、Ubuntu 和 Debian 等 Linux 分发版[提供了安装说明](https://docs.mongodb.com/manual/administration/install-on-linux/)。 以下示例创建 CentOS VM。 若要创建此环境，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例通过名为 *azureuser* 的用户使用 SSH 公钥身份验证创建名为 *myVM* 的 VM

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用你自己的用户名和上一步骤中的输出中列出的 `publicIpAddress` 以 SSH 方式登录到 VM：

```bash
ssh azureuser@<publicIpAddress>
```

若要为 MongoDB 添加安装源，请按如下所示创建一个 yum 存储库文件：

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

打开该 MongoDB 存储库文件进行编辑，例如使用 `vi` 或 `nano`。 添加以下行：

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

按如下所示使用 yum 安装 MongoDB：

```bash
sudo yum install -y mongodb-org
```

默认情况下，在阻止访问 MongoDB 的 CentOS 映像中强制实施 SELinux。 安装策略管理工具并配置 SELinux，让 MongoDB 在其默认的 TCP 端口 27017 上运行，如下所示：

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

按如下所示启动 MongoDB 服务：

```bash
sudo service mongod start
```

通过使用本地 `mongo` 客户端进行连接来验证 MongoDB 安装：

```bash
mongo
```

现在，通过添加一些数据并执行搜索来测试 MongoDB 实例：

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

如果需要，可将 MongoDB 配置为在系统重新引导期间自动启动：

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>使用模板在 CentOS 上创建基本 MongoDB 实例
可以使用 GitHub 中的以下 Azure 快速入门模板，在单个 CentOS VM 上创建基本的 MongoDB 实例。 此模板使用适用于 Linux 的自定义脚本扩展将 yum 存储库添加到新建的 CentOS VM，然后安装 MongoDB。

* [CentOS 上的基本 MongoDB 实例](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

若要创建此环境，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。 首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

接下来，使用 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) 部署 MongoDB 模板。 出现提示时，为 newStorageAccountName、dnsNameForPublicIP 以及管理员用户名和密码提供自己的唯一值：

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

使用 VM 的公共 DNS 地址登录到 VM。 可以使用 [az vm show](/cli/azure/vm#az_vm_show) 查看公共 DNS 地址：

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

使用自己的用户名和公共 DNS 地址 SSH 连接到 VM：

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

如下所示，通过使用本地 `mongo` 客户端进行连接来验证 MongoDB 安装：

```bash
mongo
```

现在，按如下所示，通过添加一些数据并执行搜索来测试实例：

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>使用模板在 CentOS 上创建复杂的 MongoDB 分片群集
可以使用 Github 中的以下 Azure 快速入门模板创建复杂的 MongoDB 分片群集。 此模板遵循 [MongoDB 分片群集最佳实践](https://docs.mongodb.com/manual/core/sharded-cluster-components/)来提供冗余和高可用性。 该模板将创建两个分片，其中每个副本集包含三个节点。 另外，它还会创建一个配置服务器副本集和两个 mongos 路由器服务器，前者包含 3 个节点的，后者用于确保各分片中的应用程序保持一致。

* [CentOS 上的 MongoDB 分片群集](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> 部署这种复杂 MongoDB 分片群集需要 20 个以上的核心（每个区域中一个订阅的默认核心计数通常为 20 个）。 请提出 Azure 支持请求，以增加核心计数。

若要创建此环境，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。 首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

接下来，使用 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) 部署 MongoDB 模板。 根据需要定义自己的资源名称和大小，例如针对 mongoAdminUsername、sizeOfDataDiskInGB 和 configNodeVmSize：

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

此部署可能需要一个多小时来部署和配置所有 VM 实例。 上述命令末尾使用了 `--no-wait` 标志，在 Azure 平台接受模板部署后将控制权返回给命令提示符。 然后，可以使用 [az group deployment show](/cli/azure/group/deployment#az_group_deployment_show) 查看部署状态。 以下示例查看 myResourceGroup 资源组中 myMongoDBCluster 部署的状态：

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>后续步骤
在上述示例中，已在本地从 VM 连接到 MongoDB 实例。 如果想要从另一个 VM 或网络连接到 MongoDB 实例，请确保[创建相应的网络安全组规则](nsg-quickstart.md)。

这些示例部署用于开发的核心 MongoDB 环境。 请为你的环境应用必需的安全配置选项。 有关详细信息，请参阅 [MongoDB 安全文档](https://docs.mongodb.com/manual/security/)。

有关使用模板创建这些规则的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。

Azure 资源管理器模板使用自定义脚本扩展在 VM 上下载并执行脚本。 有关详细信息，请参阅[在 Linux 虚拟机上使用 Azure 自定义脚本扩展](extensions-customscript.md)。

