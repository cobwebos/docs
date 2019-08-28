---
title: 在 Azure 中部署 OpenShift 容器平台自我托管的 Marketplace 服务 |Microsoft Docs
description: 在 Azure 中部署 OpenShift 容器平台自托管 Marketplace 产品/服务。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 87b5c4ab006d9129d7530b06d8b31df35e288c75
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091915"
---
# <a name="configure-prerequisites"></a>配置先决条件

在使用 Marketplace 产品/服务在 Azure 中部署自我托管的 OpenShift 容器平台群集之前, 必须先配置几个必备组件。  有关创建 ssh 密钥 (无密码)、Azure 密钥保管库、密钥保管库密钥和服务主体的说明, 请阅读[OpenShift 先决条件](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites)一文。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用 Marketplace 产品/服务进行部署

若要将自行管理的 OpenShift 容器平台群集部署到 Azure 中, 最简单的方法是使用[Azure Marketplace 产品/服务](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)。

这是最简单的方法, 但它也具有有限的自定义功能。 Marketplace 产品/服务将部署 OpenShift 容器平台 3.11.82, 并包含以下配置选项:

- **主节点**：三 (3) 个包含可配置实例类型的主节点。
- **Infra 节点**：三 (3) 个包含可配置实例类型的 Infra 节点。
- **节点**：节点数 (1 到9之间) 和实例类型都是可配置的。
- **磁盘类型**：使用托管磁盘。
- **网络**：支持新的或现有的网络和自定义的 CIDR 范围。
- **CNS**：可以启用 CNS。
- **指标**：可以启用 Hawkular 指标。
- **日志记录**：可以启用 EFK 日志记录。
- **Azure 云提供商**：默认情况下启用, 可以禁用。

在 Azure 门户的左上方, 单击 "**创建资源**", 在搜索框中输入 "openshift 容器平台", 然后按 enter。

   ![新资源搜索](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

"结果" 页将在列表中以**Red Hat OpenShift 容器平台自行管理**打开。 

   ![新资源搜索结果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

单击产品/服务可查看产品/服务的详细信息。 若要部署此产品/服务, 请单击 "**创建**"。 将出现用于输入必要参数的 UI。 第一个屏幕是 "**基本**信息" 边栏选项卡。

   ![产品/服务标题页](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基础知识**

若要获取有关任何输入参数的帮助, 请将鼠标悬停在参数名称旁边的***i*** 。

输入输入参数的值, 然后单击 **"确定"** 。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| VM 管理员用户名 | 要在所有 VM 实例上创建的管理员用户 |
| 管理员用户的 SSH 公钥 | 用于登录到 VM 的 SSH 公钥-不得具有密码 |
| 订阅 | 要将群集部署到的 Azure 订阅 |
| 资源组 | 为群集资源创建新的资源组或选择现有的空资源组 |
| Location | 要在其中部署群集的 Azure 区域 |

   ![提供基本信息边栏选项卡](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基础结构设置**

输入输入参数的值, 然后单击 **"确定"** 。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| OCP 群集名称前缀 | 用于配置所有节点的主机名的群集前缀。 介于1到20个字符之间 |
| 主节点大小 | 接受默认 VM 大小或单击 "**更改大小**" 以选择不同的 vm 大小。  为工作负荷选择合适的 VM 大小 |
| 基础结构节点大小 | 接受默认 VM 大小或单击 "**更改大小**" 以选择不同的 vm 大小。  为工作负荷选择合适的 VM 大小 |
| 应用程序节点数 | 接受默认 VM 大小或单击 "**更改大小**" 以选择不同的 vm 大小。  为工作负荷选择合适的 VM 大小 |
| 应用程序节点大小 | 接受默认 VM 大小或单击 "**更改大小**" 以选择不同的 vm 大小。  为工作负荷选择合适的 VM 大小 |
| 堡垒主机大小 | 接受默认 VM 大小或单击 "**更改大小**" 以选择不同的 vm 大小。  为工作负荷选择合适的 VM 大小 |
| 新的或现有的虚拟网络 | 创建新的 vNet (默认值) 或使用现有的 vNet |
| 选择默认 CIDR 设置或自定义 IP 范围 (CIDR) | 接受默认的 CIDR 范围, 或选择 "**自定义 IP 范围**" 并输入自定义 CIDR 信息。  默认设置将创建具有子网 10.0.0.0/14 的 vNet、带有 10.1.0.0/16 的子网、带有 10.2.0.0/16 的基础网, 以及带有 10.3.0.0/16 的计算和 cns 子网 |
| Key Vault 资源组名称 | 包含 Key Vault 的资源组的名称 |
| Key Vault 名称 | 包含具有 ssh 私钥的机密 Key Vault 的名称。  只允许使用字母数字字符和短划线, 并且长度必须介于3到24个字符之间 |
| 机密名称 | 包含 ssh 私钥的机密的名称。  只允许使用字母数字字符和短划线 |

   ![提供基础结构边栏选项卡](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**更改大小**

若要选择不同的 VM 大小, 请单击 "***更改大小***"。  将打开 "VM 选择" 窗口。  选择所需的 VM 大小, 并单击 "**选择**"。

   ![选择 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**现有虚拟网络**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 现有虚拟网络名称 | 现有 vNet 的名称 |
| 主节点的子网名称 | 主节点的现有子网的名称。  需要至少包含16个 IP 地址, 并遵循 RFC 1918 |
| 基础节点的子网名称 | 基础节点的现有子网的名称。  需要至少包含32个 IP 地址, 并遵循 RFC 1918 |
| 计算和 cns 节点的子网名称 | 计算节点和 cns 节点的现有子网的名称。  需要至少包含32个 IP 地址, 并遵循 RFC 1918 |
| 现有虚拟网络的资源组 | 包含现有 vNet 的资源组的名称 |

   ![提供基础结构现有 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自定义 IP 范围**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 虚拟网络的地址范围 | VNet 的自定义 CIDR |
| 包含主节点的子网的地址范围 | 子网的自定义 CIDR |
| 包含基础结构节点的子网的地址范围 | 基础结构子网的自定义 CIDR |
| 包含计算和 cns 节点的子网的地址范围 | 计算和 cns 节点的自定义 CIDR |

   ![提供基础结构自定义 IP 范围](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift 容器平台**

输入输入参数的值, 然后单击 **"确定"**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| OpenShift 管理员用户密码 | 初始 OpenShift 用户的密码。  此用户也将是群集管理员 |
| 确认 OpenShift 管理员用户密码 | 重新键入 OpenShift 管理员用户密码 |
| Red Hat 订阅管理器用户名 | 用于访问 Red Hat 订阅或组织 ID 的用户名。  此凭据用于向你的订阅注册 RHEL 实例, 且不会由 Microsoft 或 Red Hat 存储 |
| Red Hat 订阅管理器用户密码 | 用于访问 Red Hat 订阅或激活密钥的密码。  此凭据用于向你的订阅注册 RHEL 实例, 且不会由 Microsoft 或 Red Hat 存储 |
| Red Hat 订阅管理器 OpenShift 池 ID | 包含 OpenShift 容器平台权利的池 ID。 确保有足够的 OpenShift 容器平台权利用于安装群集 |
| Red Hat 订阅管理器 OpenShift 池 ID | 包含 Broker/主节点的 OpenShift 容器平台权利的池 ID。 确保有足够的 OpenShift 容器平台权利用于安装群集。 如果不使用 broker/主池 ID, 请输入应用程序节点的池 ID |
| 配置 Azure 云提供程序 | 将 OpenShift 配置为使用 Azure 云提供程序。 如果对持久卷使用 Azure 磁盘附加则需要。  默认值为 "是" |
| Azure AD 服务主体客户端 ID GUID | Azure AD 服务主体客户端 ID GUID-也称为 AppID。 仅在将 Azure 云提供程序设置为 **"是"** 时需要 |
| Azure AD 服务主体客户端 ID 机密 | Azure AD 的服务主体客户端 ID 机密。 仅在将 Azure 云提供程序设置为 **"是"** 时需要 |
 
   ![提供 OpenShift 边栏选项卡](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他设置**

"其他设置" 边栏选项卡允许配置 glusterfs 存储、日志记录、指标和路由器子域。  默认情况下不安装任何这些选项, 将使用 nip.io 作为路由器子域以进行测试。 启用 CN 后, 将安装三个额外的计算节点, 其中包含另外三个附加的磁盘, 将托管 glusterfs pod。  

输入输入参数的值, 然后单击 **"确定"**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 配置容器本机存储 (CNS) | 在 OpenShift 群集中安装 CNS, 并将其启用为存储。 如果 Azure 提供程序已禁用, 则默认值为 |
| 配置群集日志记录 | 在群集中安装 EFK 日志记录功能。  适当调整托管节点的大小以托管 EFK pod |
| 配置群集的指标 | 将 Hawkular 度量值安装到 OpenShift 群集中。  适当调整托管节点的大小以托管 Hawkular 指标 pod |
| 默认路由器子域 | 选择 nipio 进行测试, 或选择 "自定义" 输入你自己的用于生产的子域 |
 
   ![提供其他边栏选项卡](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**其他设置-额外参数**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| CN节点大小 | 接受默认节点大小, 或选择 "**更改大小**" 以选择新的 VM 大小 |
| 输入自定义子域 | 要用于通过 OpenShift 群集上的路由器公开应用程序的自定义路由域。  请确保创建相应的通配符 DNS 条目] |
 
   ![提供其他的 cns 安装](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**摘要**

在此阶段进行验证, 以检查核心配额是否足以部署为群集选择的 Vm 的总数。  查看输入的所有参数。  如果输入可接受, 请单击 **"确定"** 继续。

   ![产品/服务摘要边栏选项卡](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**购买**

确认 "购买" 页上的联系人信息, 然后单击 "**购买**" 以接受 OpenShift 容器平台群集的使用条款和开始部署。

   ![提供购买边栏选项卡](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 使用**OpenShift 控制台 URL**, 通过浏览器连接到 OpenShift 控制台。 还可以通过 SSH 连接到堡垒主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-troubleshooting.md)
- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform)

