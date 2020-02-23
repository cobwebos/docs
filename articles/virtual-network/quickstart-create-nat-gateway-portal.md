---
title: 快速入门：创建 NAT 网关 - Azure 门户
titlesuffix: Azure Virtual Network NAT
description: 本快速入门介绍如何使用 Azure 门户创建 NAT 网关
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 8913c956554fa7bf1d0362b44dc6f8031ffd74f9
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429128"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建 NAT 网关

本快速入门介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](./nat-overview.md#region-availability)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="sign-in-to-azure"></a>登录 Azure

> [!IMPORTANT]
> 在订阅中启用虚拟网络 NAT [预览版](./nat-overview.md#enable-preview)后，使用 https://aka.ms/natportal 访问门户。

登录 [Azure 门户](https://aka.ms/natportal)。


### <a name="create-a-virtual-network"></a>创建虚拟网络

在部署 VM 并使用 NAT 网关之前，需要创建资源组和虚拟网络。  

1. 在屏幕的左上方选择“创建资源” > “网络” > “虚拟网络”，或者在市场搜索中搜索“虚拟网络”。    

2. 在“创建虚拟网络”  中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 **myVNet**。 |
    | 地址空间 | 输入 192.168.0.0/16  。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”并输入 **myResourceGroupNAT**。 |
    | 位置 | 选择“美国东部 2”。 |
    | 子网 - 名称 | 输入 **mySubnet**。 |
    | 子网 - 地址范围 | 输入 **192.168.0.0/24**。 |

3. 将剩余的字段保留默认设置，然后选择 **“创建”** 。

### <a name="create-a-vm-to-use-the-nat-gateway"></a>创建 VM 以使用 NAT 网关

现在，我们将创建一个 VM 来使用 NAT 服务。 此 VM 将某个公共 IP 用作实例级公共 IP，使你能够访问此 VM。 NAT 服务可识别流的方向，并会替代子网中的默认 Internet 目标。 VM 的公共 IP 地址不会用于出站连接。

1. 在门户的左上方选择“创建资源” > “计算” > “Ubuntu Server 18.04 LTS”，或者在市场搜索中搜索“Ubuntu Server 18.04 LTS”。    

2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”。 
   - **实例详细信息** > **虚拟机名称**：键入 **myVM**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **管理员帐户** > **身份验证类型**：选择“密码”。 
   - **管理员帐户**：输入“用户名”、“密码”和“确认密码”信息。   
   - **入站端口规则** > **公共入站端口**：选择“允许所选端口”  。
   - **入站端口规则** > **选择入站端口**：选择“SSH (22)” 
   - 选择“网络”  选项卡，或选择“下一步: **磁盘”，然后选择“下一步:**  网络”。

3. 在“网络”选项卡中，确保选中以下项  ：
   - **虚拟网络**：**myVnet**
   - **子网**：**mySubnet**
   - **公共 IP**：选择“新建”。   在“创建公共 IP 地址”窗口中，在“名称”字段中键入 **myPublicIPVM**，为“SKU”选择“标准”。      单击“确定”。 
   - **NIC 网络安全组**：选择“基本”。 
   - **公共入站端口**：选择“允许所选端口”  。
   - **选择入站端口**：确认已选择“SSH”。 

4. 在“管理”选项卡的“监视”下，将“启动诊断”设置为“关闭”     。

5. 选择“查看 + 创建”  。 

6. 检查设置并单击“创建”。 

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

可以使用一个或多个公共 IP 地址资源和/或公共 IP 前缀。 我们将添加公共 IP 资源、公共 IP 前缀和 NAT 网关资源。

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
    | 名称 | 输入 **myPublicIP**。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“myResourceGroupNAT”。  |
    | 位置 | 选择“美国东部 2”。 |

3. 将剩余的字段保留默认设置，然后选择 **“创建”** 。

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

1. 在门户的左上方选择“创建资源” > “网络” > “公共 IP 前缀”，或者在市场搜索中搜索“公共 IP 前缀”。     

2. 在“创建公共 IP 前缀”中，在“基本信息”选项卡中键入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”  >
   - **实例详细信息** > **名称**：键入 **myPublicIPprefix**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **实例详细信息** > **前缀大小**：选择“/31 (2 个地址)” 

3. 将剩余的字段保留默认值，然后选择“查看 + 创建”。 

4. 检查设置，然后选择“创建”。 
   

### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

1. 在门户的左上方选择“创建资源” > “网络” > “NAT 网关”，或者在市场搜索中搜索“NAT 网关”。    

2. 在“创建网络地址转换(NAT)网关”中，在“基本信息”选项卡中键入或选择以下值：  
   - **订阅** > **资源组**：选择“myResourceGroupNAT”。 
   - **实例详细信息** > **NAT 网关名称**：键入 **myNATgateway**。
   - **实例详细信息** > **区域**：选择“美国东部 2”。 
   - **实例详细信息** > **空闲超时(分钟)** ：键入 **10**。
   - 选择“公共 IP”选项卡，或选择“下一步:   公共 IP”。

3. 在“公共 IP”选项卡中，键入或选择以下值： 
   - **公共 IP 地址**：选择“myPublicIP”。 
   - **公共 IP 前缀**：选择“myPublicIPprefix”。 
   - 选择“子网”选项卡，或选择“下一步:   子网”。

4. 在“子网”选项卡中，键入或选择以下值： 
   - **虚拟网络**：选择“myResourceGroupNAT” > “myVnet”。  
   - **子网名称**：选中“mySubnet”旁边的复选框。 

5. 选择“查看 + 创建”  。

6. 检查设置，然后选择“创建”。 

## <a name="discover-the-ip-address-of-the-vm"></a>发现 VM 的 IP 地址

1. 在门户左侧选择“资源组”。 
2. 选择“myResourceGroupNAT”。 
3. 选择“myVM”。 
4. 在“概述”中，复制“公共 IP 地址”值并将其粘贴到记事本中，以便可以用它访问 VM。  

>[!IMPORTANT]
>复制公共 IP 地址并将其粘贴到记事本中，以便可以用它来访问 VM。

## <a name="sign-in-to-vm"></a>登录到 VM

在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

现已准备好使用 NAT 服务。

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组、NAT 网关和所有相关资源，请将其删除。 选择包含 NAT 网关的资源组 **myResourceGroupNAT**，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 NAT 网关，并创建了一个 VM 来使用该网关。 

可以查看 Azure Monitor 中的指标来了解 NAT 服务的运行情况。 可以诊断可用 SNAT 端口资源耗尽等问题。  添加更多公共 IP 地址资源和/或公共 IP 前缀资源即可解决 SNAT 端口资源耗尽的问题。


- 了解 [Azure 虚拟网络 NAT](./nat-overview.md)
- 了解 [NAT 网关资源](./nat-gateway-resource.md)。
- 有关[使用 Azure CLI 部署 NAT 网关资源](./quickstart-create-nat-gateway-cli.md)的快速入门。
- 有关[使用 Azure PowerShell 部署 NAT 网关资源](./quickstart-create-nat-gateway-powershell.md)的快速入门。
- 有关[使用 Azure 门户部署 NAT 网关资源](./quickstart-create-nat-gateway-portal.md)的快速入门。
- [提供有关公共预览版的反馈](https://aka.ms/natfeedback)。
> [!div class="nextstepaction"]

