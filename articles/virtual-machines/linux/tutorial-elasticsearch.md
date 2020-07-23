---
title: 在 Azure 中的开发虚拟机上部署 Elasticsearch
description: 教程 - 在 Azure 中的开发 Linux VM 上安装 Elastic Stack
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 1b7b4d3c25794a62bc19925ade278159ebb37615
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80066545"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>在 Azure VM 上安装 Elastic Stack

本文逐步讲解如何在 Azure 中的 Ubuntu VM 上部署 [Elasticsearch](https://www.elastic.co/products/elasticsearch)、[Logstash](https://www.elastic.co/products/logstash) 和 [Kibana](https://www.elastic.co/products/kibana)。 要查看实际操作中的 Elastic Stack，可选择连接到 Kibana 并处理某些示例日志记录数据。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 资源组中创建 Ubuntu VM
> * 在 VM 上安装 Elasticsearch、Logstash 和 Kibana
> * 使用 Logstash 将示例数据发送到 Elasticsearch 
> * 在 Kibana 控制台中打开端口并处理数据


 此部署适用于使用 Elastic Stack 进行基本开发的情况。 有关 Elastic 堆栈的详细信息，包括针对生产环境的建议，请参阅 [Elastic 文档](https://www.elastic.co/guide/index.html)和 [Azure 体系结构中心](/azure/architecture/elasticsearch/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

下面的示例创建一个名为 *myVM* 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 记下 `publicIpAddress`。 此地址用于访问 VM。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

如果还不知道 VM 的公共 IP 地址，请运行 [az network public-ip list](/cli/azure/network/public-ip) 命令：

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

使用以下命令创建与虚拟机的 SSH 会话。 使用虚拟机的正确的公共 IP 地址进行替换。 在此示例中，IP 地址为 *40.68.254.142*。

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>安装 Elastic Stack

导入 Elasticsearch 签名密钥并更新 APT 源列表，以包括 Elastic 包存储库：

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

在 VM 上安装 Java Virtual 并配置 JAVA_HOME 变量 - 这是运行 Elastic Stack 组件所必需的。

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

运行以下命令更新 Ubuntu 包源并安装 Elasticsearch、Kibana 和 Logstash。

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> [Elastic 文档](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)中详述了安装说明，包括目录布局和初始配置

## <a name="start-elasticsearch"></a>启动 Elasticsearch 

使用以下命令启动 VM 上的 Elasticsearch：

```bash
sudo systemctl start elasticsearch.service
```

此命令不会产生输出，因此请使用 `curl` 命令验证该 Elasticsearch 是否在 VM 上运行：

```bash
sudo curl -XGET 'localhost:9200/'
```

如果 Elasticsearch 在运行，则会看到如下所示的输出：

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>启动 Logstash 并将数据添加到 Elasticsearch

使用以下命令启动 Logstash：

```bash 
sudo systemctl start logstash.service
```

在交互模式下测试 Logstash，确保它正确运行：

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

这是基本 logstash [管道](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html)，它将标准输入回响到标准输出。 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

设置 Logstash，将内核消息从此 VM 转发到 Elasticsearch。 在名为 `vm-syslog-logstash.conf` 的空目录中新建文件，并粘贴到以下 Logstash 配置中：

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

测试此配置，然后将 syslog 数据发送到 Elasticsearch：

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

将 syslog 项发送到 Elasticsearch 时回响的终端中会显示这些 syslog 项。 发送一些数据后，请使用 `CTRL+C` 退出 Logstash。

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>启动 Kibana 并在 Elasticsearch 中直观显示数据

编辑 `/etc/kibana/kibana.yml` 并更改 Kibana 侦听的 IP 地址，以便通过 Web 浏览器对其进行访问。

```bash
server.host:"0.0.0.0"
```

使用以下命令启动 Kibana：

```bash
sudo systemctl start kibana.service
```

通过 Azure CLI 打开端口 5601，允许远程访问 Kibana 控制台：

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

打开 Kibana 控制台并选择“创建”，根据之前发送给 Elasticsearch 的 syslog 数据生成默认索引  。 

![浏览 Kibana 中的 Syslog 事件](media/elasticsearch-install/kibana-index.png)

在 Kibana 控制台上选择“发现”，搜索、浏览和筛选 syslog 事件  。

![浏览 Kibana 中的 Syslog 事件](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>后续步骤

本教程已将 Elastic Stack 部署到 Azure 中的开发 VM。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 资源组中创建 Ubuntu VM
> * 在 VM 上安装 Elasticsearch、Logstash 和 Kibana
> * 通过 Logstash 将示例数据发送到 Elasticsearch 
> * 在 Kibana 控制台中打开端口并处理数据
