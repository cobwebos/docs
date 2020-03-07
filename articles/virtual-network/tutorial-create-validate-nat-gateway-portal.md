---
title: 教程：创建并测试 NAT 网关 - Azure 门户
titlesuffix: Azure Virtual Network NAT
description: 本教程介绍如何使用 Azure 门户创建 NAT 网关并测试 NAT 服务
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 4baf12533bed523c81ff41a81975f5bf5b918ac2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250805"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>教程：使用 Azure 门户创建 NAT 网关并测试 NAT 服务

在本教程中，你将创建一个 NAT 网关来为 Azure 中的虚拟机提供出站连接。 为了测试该 NAT 网关，你将部署源和目标虚拟机。 你将通过从源虚拟机的公共 IP 地址与目标虚拟机建立出站连接，来测试 NAT 网关。  为简单起见，本教程将源和目标部署在同一资源组中的两个不同虚拟网络内。

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](./nat-overview.md#region-availability)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="prepare-the-source-for-outbound-traffic"></a>准备出站流量的源

后续步骤将引导你配置整个测试环境，并执行测试本身。 我们从源开始。源使用我们在后续步骤中创建的 NAT 网关资源。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在部署 VM 并使用 NAT 网关之前，需要创建资源组和虚拟网络。

在本部分中，你需要将步骤中的以下参数替换为以下信息：

| 参数                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | 美国东部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>创建源虚拟机

现在，我们将创建一个 VM 来使用 NAT 服务。 此 VM 将某个公共 IP 用作实例级公共 IP，使你能够访问此 VM。 NAT 服务可识别流的方向，并会替代子网中的默认 Internet 目标。 VM 的公共 IP 地址不会用于出站连接。

为了测试 NAT 网关，我们将某个公共 IP 地址资源分配为实例级公共 IP，以便从外部访问此 VM。 此地址仅用于访问此 VM，以进行测试。  我们将演示 NAT 服务如何优先于其他出站选项。

也可以不使用公共 IP 创建此 VM，而是在练习中创建另一个 VM 作为没有公共 IP 的 Jumpbox。

1. 在门户的左上方选择“创建资源” > “计算” > “Ubuntu Server 18.04 LTS”，或者在市场搜索中搜索“Ubuntu Server 18.04 LTS”。    

2. 在“创建虚拟机”中，在“基本信息”选项卡中输入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”。 
   - **实例详细信息** > **虚拟机名称**：输入 **myVMsource**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **管理员帐户** > **身份验证输入**：选择“密码”。 
   - **管理员帐户**：输入“用户名”、“密码”和“确认密码”信息。   
   - **入站端口规则** > **公共入站端口**：选择“允许所选端口”  。
   - **入站端口规则** > **选择入站端口**：选择“SSH (22)” 
   - 选择“网络”  选项卡，或选择“下一步: **磁盘”，然后选择“下一步:**  网络”。

3. 在“网络”选项卡中，确保选中以下项  ：
   - **虚拟网络**：**myVnetsource**
   - **子网**：**mySubnetsource**
   - **公共 IP**：选择“新建”。   在“创建公共 IP 地址”窗口中的“名称”字段内输入 **myPublicIPsourceVM**。   为“SKU”选择“标准”。   将剩余的字段保留默认值，然后单击“确定”。 
   - **NIC 网络安全组**：选择“基本”。 
   - **公共入站端口**：选择“允许所选端口”  。
   - **选择入站端口**：确认已选择“SSH”。 

4. 在“管理”选项卡的“监视”下，将“启动诊断”设置为“关闭”     。

5. 选择“查看 + 创建”  。

6. 检查设置并单击“创建”。 

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

可对 NAT 网关使用一个或多个公共 IP 地址资源和/或公共 IP 前缀。 我们将添加公共 IP 资源、公共 IP 前缀和 NAT 网关资源。

本部分详细介绍如何使用 NAT 网关资源创建并配置 NAT 服务的以下组件：
  - 一个公共 IP 池和公共 IP 前缀，供 NAT 网关资源转换的出站流使用。
  - 将空闲超时从默认值 4 分钟更改为 10 分钟。

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

1. 在门户的左上方选择“创建资源” > “网络” > “公共 IP 地址”，或者在市场搜索中搜索“公共 IP 地址”。     

2. 在“创建公共 IP 地址”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | IP 版本 | 选择“IPv4”。 
    | SKU | 选择“标准”  。
    | 名称 | 输入 **myPublicIPsource**。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“myResourceGroupNAT”。  |
    | 位置 | 选择“美国东部 2”。 |

3. 将剩余的字段保留默认设置，然后选择 **“创建”** 。

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

1. 在门户的左上方选择“创建资源” > “网络” > “公共 IP 前缀”，或者在市场搜索中搜索“公共 IP 前缀”。    

2. 在“创建公共 IP 前缀”中，在“基本信息”选项卡中输入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”  >
   - **实例详细信息** > **名称**：输入 **myPublicIPprefixsource**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **实例详细信息** > **前缀大小**：选择“/31 (2 个地址)” 

3. 将剩余的字段保留默认值，然后选择“查看 + 创建”。 

4. 检查设置，然后选择“创建”。 


### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

1. 在门户的左上方选择“创建资源” > “网络” > “NAT 网关”，或者在市场搜索中搜索“NAT 网关”。    

2. 在“创建网络地址转换(NAT)网关”中，在“基本信息”选项卡中输入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”。 
   - **实例详细信息** > **NAT 网关名称**：输入 **myNATgateway**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **实例详细信息** > **空闲超时(分钟)** ：输入 **10**。
   - 选择“公共 IP”选项卡，或选择“下一步:   公共 IP”。

3. 在“公共 IP”选项卡中，输入或选择以下值： 
   - **公共 IP 地址**：选择“myPublicIPsource”。 
   - **公共 IP 前缀**：选择“myPublicIPprefixsource”。 
   - 选择“子网”选项卡，或选择“下一步:   子网”。

4. 在“子网”选项卡中，输入或选择以下值： 
   - **虚拟网络**：选择“myResourceGroupNAT” > “myVnetsource”。  
   - **子网名称**：选中“mySubnetsource”旁边的复选框。 

5. 选择“查看 + 创建”  。

6. 检查设置，然后选择“创建”。 

发往 Internet 目标的所有出站流量现在将使用该 NAT 服务。  无需配置 UDR。


## <a name="prepare-destination-for-outbound-traffic"></a>准备出站流量的目标

现在，我们将为 NAT 服务转换的出站流量创建目标，以便对其进行测试。


## <a name="virtual-network-and-parameters-for-destination"></a>目标的虚拟网络和参数

在为目标部署 VM 之前，需要创建一个虚拟网络，可将目标虚拟机置于其中。 以下步骤与针对源 VM 执行的步骤相同，只是需要做出一些轻微的更改来公开目标终结点。

在本部分中，你需要将步骤中的以下参数替换为以下信息：

| 参数                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | 美国东部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>创建目标虚拟机

1. 在门户的左上方选择“创建资源” > “计算” > “Ubuntu Server 18.04 LTS”，或者在市场搜索中搜索“Ubuntu Server 18.04 LTS”。    

2. 在“创建虚拟机”中，在“基本信息”选项卡中输入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”。 
   - **实例详细信息** > **虚拟机名称**：输入 **myVMdestination**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **管理员帐户** > **身份验证输入**：选择“密码”。 
   - **管理员帐户**：输入“用户名”、“密码”和“确认密码”信息。   
   - **入站端口规则** > **公共入站端口**：选择“允许所选端口”  。
   - **入站端口规则** > **选择入站端口**：选择“SSH (22)”和“HTTP (80)”。  
   - 选择“网络”  选项卡，或选择“下一步: **磁盘”，然后选择“下一步:**  网络”。

3. 在“网络”选项卡中，确保选中以下项  ：
   - **虚拟网络**：**myVnetdestination**
   - **子网**：**mySubnetdestination**
   - **公共 IP**：选择“新建”。   在“创建公共 IP 地址”窗口中的“名称”字段内输入 **myPublicIPdestinationVM**。   为“SKU”选择“标准”。   将剩余的字段保留默认值，然后单击“确定”。 
   - **NIC 网络安全组**：选择“基本”。 
   - **公共入站端口**：选择“允许所选端口”  。
   - **选择入站端口**：确认已选择“SSH”和“HTTP”。  

4. 在“管理”选项卡的“监视”下，将“启动诊断”设置为“关闭”     。

5. 选择“查看 + 创建”  。

6. 检查设置，然后选择“创建”。 

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目标 VM 上准备 Web 服务器和测试有效负载

首先需要发现目标 VM 的 IP 地址。 

1. 在门户左侧选择“资源组”。 
2. 选择“myResourceGroupNAT”。 
3. 选择“myVMdestination”。 
4. 在“概述”中，复制“公共 IP 地址”值并将其粘贴到记事本中，以便可以用它访问 VM。  

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是目标虚拟机。

### <a name="sign-in-to-destination-vm"></a>登录到目标 VM

在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

登录后，复制并粘贴以下命令。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

这些命令将更新虚拟机，安装 nginx，并创建 100 KB 大小的文件。 将使用 NAT 服务从源 VM 中检索此文件。

关闭与目标 VM 建立的 SSH 会话。

## <a name="prepare-test-on-source-vm"></a>在源 VM 上准备测试

首先需要发现源 VM 的 IP 地址。

1. 在门户左侧选择“资源组”。 
2. 选择“myResourceGroupNAT”。 
3. 选择“myVMsource”。 
4. 在“概述”中，复制“公共 IP 地址”值并将其粘贴到记事本中，以便可以用它访问 VM。  

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是源虚拟机。

### <a name="log-into-source-vm"></a>登录到源 VM

在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com) 的新选项卡。  使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

复制并粘贴以下命令，以准备测试 NAT 服务。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

此命令将更新虚拟机，安装 go，安装 GitHub 中的 [hey](https://github.com/rakyll/hey)，并更新 shell 环境。

现已准备好测试 NAT 服务。

## <a name="validate-nat-service"></a>验证 NAT 服务

登录到源 VM 后，可以使用 **curl** 和 **hey** 生成发往目标 IP 地址的请求。

使用 curl 检索 100 KB 大小的文件。  请将以下示例中的 **\<ip-address-destination>** 替换为前面复制的目标 IP 地址。  **--output** 参数指示将丢弃检索到的文件。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

也可以使用 **hey** 生成一系列请求。 同样，请将 **\<ip-address-destination>** 替换为前面复制的目标 IP 地址。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

此命令将生成 100 个请求，其中有 10 个是超时为 30 秒且不重复使用 TCP 连接的并发请求。  每个请求将检索 100 KB。  运行结束时，**hey** 会报告有关 NAT 服务运行情况的统计信息。

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组、NAT 网关和所有相关资源，请将其删除。 选择包含 NAT 网关的资源组 **myResourceGroupNAT**，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤
在本教程中，你已创建 NAT 网关、源 VM 和目标 VM，然后测试了 NAT 网关。

可以查看 Azure Monitor 中的指标来了解 NAT 服务的运行情况。 可以诊断可用 SNAT 端口资源耗尽等问题。  通过添加更多公共 IP 地址资源和/或公共 IP 前缀资源，可以轻松解决 SNAT 端口资源耗尽的问题。

- 了解[虚拟网络 NAT](./nat-overview.md)。
- 了解 [NAT 网关资源](./nat-gateway-resource.md)。
- 有关[使用 Azure CLI 部署 NAT 网关资源](./quickstart-create-nat-gateway-cli.md)的快速入门。
- 有关[使用 Azure PowerShell 部署 NAT 网关资源](./quickstart-create-nat-gateway-powershell.md)的快速入门。
- 有关[使用 Azure 门户部署 NAT 网关资源](./quickstart-create-nat-gateway-portal.md)的快速入门。
- [提供有关公共预览版的反馈](https://aka.ms/natfeedback)。

> [!div class="nextstepaction"]

