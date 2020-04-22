---
title: 在 Azure 中部署 OpenShift 容器平台 3.11 自我管理的市场产品
description: 在 Azure 中部署 OpenShift 容器平台 3.11 自管理应用商店产品。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759574"
---
# <a name="configure-prerequisites"></a>配置先决条件

在使用应用商店产品/服务在 Azure 中部署自管理的 OpenShift 容器平台 3.11 群集之前，必须配置一些先决条件。  阅读[OpenShift 先决条件](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites)一文，了解创建 ssh 密钥（不带密码短语）、Azure 密钥保管库、密钥保管库密钥密钥和服务主体的说明。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用应用商店产品/服务进行部署

将自管理的 OpenShift 容器平台 3.11 群集部署到 Azure 的最简单方法是使用[Azure 应用商店产品/](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

此选项是最简单的，但它也具有有限的自定义功能。 应用商店产品部署 OpenShift 容器平台 3.11.82，包括以下配置选项：

- **主节点**：三 (3) 个包含可配置实例类型的主节点。
- **Infra 节点**：三 (3) 个包含可配置实例类型的 Infra 节点。
- **节点**： 节点数（介于 1 和 9 之间）和实例类型是可配置的。
- **磁盘类型**：使用托管磁盘。
- **网络**：支持新的或现有的网络和自定义 CIDR 范围。
- **CNS**：可以启用 CNS。
- **指标**：可以启用鹰派指标。
- **日志记录**：可以启用 EFK 日志记录。
- **Azure 云提供程序**：默认情况下启用，可以禁用。

在 Azure 门户的左上角，单击 **"创建资源**"，在搜索框中输入"开班容器平台"，然后单击 Enter。

   ![新资源搜索](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

结果页面将在列表中打开**红帽 OpenShift 容器平台 3.11 自我管理**。 

   ![新的资源搜索结果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

单击产品/服务以查看优惠的详细信息。 要部署此产品/服务，请单击"**创建**"。 将显示要输入必要参数的 UI。 第一个屏幕是 **"基本"** 边栏选项卡。

   ![优惠标题页](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基础**

要获取有关任何输入参数的帮助，请将鼠标悬停在参数名称旁边的***i***上。

输入输入参数的值，然后单击 **"确定**"。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| VM 管理员用户名 | 要在所有 VM 实例上创建的管理员用户 |
| 用于管理员用户的 SSH 公钥 | 用于登录 VM 的 SSH 公钥 - 不得有密码 |
| 订阅 | 要将群集部署到 |
| 资源组 | 创建新资源组或为群集资源选择现有空资源组 |
| 位置 | 要将群集部署到 |

   ![提供基本刀片](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基础设施设置**

输入输入参数的值，然后单击 **"确定**"。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| OCP 群集名称前缀 | 用于配置所有节点的主机名的群集前缀。 1 到 20 个字符之间 |
| 主节点大小 | 接受默认 VM 大小或单击 **"更改大小"** 以选择其他 VM 大小。  为工作负载选择适当的 VM 大小 |
| 基础结构节点大小 | 接受默认 VM 大小或单击 **"更改大小"** 以选择其他 VM 大小。  为工作负载选择适当的 VM 大小 |
| 应用程序节点数 | 接受默认 VM 大小或单击 **"更改大小"** 以选择其他 VM 大小。  为工作负载选择适当的 VM 大小 |
| 应用程序节点大小 | 接受默认 VM 大小或单击 **"更改大小"** 以选择其他 VM 大小。  为工作负载选择适当的 VM 大小 |
| 堡垒主机大小 | 接受默认 VM 大小或单击 **"更改大小"** 以选择其他 VM 大小。  为工作负载选择适当的 VM 大小 |
| 新建或现有虚拟网络 | 创建新 vNet（默认）或使用现有 vNet |
| 选择默认 CIDR 设置或自定义 IP 范围 （CIDR） | 接受默认 CIDR 范围或选择**自定义 IP 范围**并输入自定义 CIDR 信息。  默认设置将创建具有 10.0.0.0/14 的 CIDR 的 vNet，使用 10.1.0.0/16 的主子网、包含 10.2.0.0/16 的红外线网以及 10.3.0.0/16 的计算和 cns 子网 |
| 密钥保管库资源组名称 | 包含密钥保管库的资源组的名称 |
| Key Vault 名称 | 包含 ssh 私钥的机密的密钥保管库的名称。  只允许字母数字字符和破折号，介于 3 到 24 个字符之间 |
| 秘密名称 | 包含 ssh 私钥的机密的名称。  仅允许字母数字字符和破折号 |

   ![提供基础架构刀片](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**更改大小**

要选择其他 VM 大小，请单击"***更改大小***"。  将打开 VM 选择窗口。  选择所需的 VM 大小，然后单击 **"选择**"。

   ![选择 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**现有虚拟网络**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 现有虚拟网络名称 | 现有 vNet 的名称 |
| 主节点的子网名称 | 主节点的现有子网的名称。  需要包含至少 16 个 IP 地址，并遵循 RFC 1918 |
| 红外线节点的子网名称 | 现有子网的子网的名称为红外线节点。  需要包含至少 32 个 IP 地址，并遵循 RFC 1918 |
| 用于计算和 cns 节点的子网名称 | 用于计算和 cns 节点的现有子网的名称。  需要包含至少 32 个 IP 地址，并遵循 RFC 1918 |
| 现有虚拟网络的资源组 | 包含现有 vNet 的资源组的名称 |

   ![提供基础设施现有 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自定义 IP 范围**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 虚拟网络的地址范围 | vNet 的自定义 CIDR |
| 包含主节点的子网的地址范围 | 主子网的自定义 CIDR |
| 包含基础结构节点的子网的地址范围 | 基础结构子网的自定义 CIDR |
| 包含计算节点和 cns 节点的子网的地址范围 | 计算节点和 cns 节点的自定义 CIDR |

   ![提供基础设施自定义 IP 范围](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift 容器平台 3.11**

输入输入参数的值，然后单击"**确定"**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 开放移位管理员用户密码 | 初始 OpenShift 用户的密码。  此用户也将是群集管理员 |
| 确认开放移位管理员用户密码 | 重新键入 OpenShift 管理员用户密码 |
| 红帽订阅管理器用户名 | 访问红帽订阅或组织 ID 的用户名。  此凭据用于将 RHEL 实例注册到您的订阅，并且不会由 Microsoft 或红帽存储 |
| 红帽订阅管理器用户密码 | 访问红帽订阅或激活密钥的密码。  此凭据用于将 RHEL 实例注册到您的订阅，并且不会由 Microsoft 或红帽存储 |
| 红帽订阅管理器打开移位池 ID | 包含 OpenShift 容器平台授权的池 ID。 确保您有足够的 OpenShift 容器平台权利来安装群集 |
| 红帽订阅管理器为经纪商/主节点打开 Shift 池 ID | 包含代理/主节点的 OpenShift 容器平台权限的池 ID。 确保您有足够的 OpenShift 容器平台权利来安装群集。 如果不使用代理/主池 ID，请输入应用程序节点的池 ID |
| 配置 Azure 云提供程序 | 将 OpenShift 配置为使用 Azure 云提供程序。 如果对持久卷使用 Azure 磁盘附加，则需要。  默认值为"是" |
| Azure AD 服务主客户端 ID GUID | Azure AD 服务主体客户端 ID GUID - 也称为 AppID。 仅当将 Azure 云提供程序设置为 **"是**"时才需要 |
| Azure AD 服务主体客户端 ID 密钥 | Azure AD 服务主体客户端 ID 密钥。 仅当将 Azure 云提供程序设置为 **"是**"时才需要 |
 
   ![提供开放式移位刀片](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他设置**

"附加设置"边栏选项卡允许配置 CNS 用于 glusterfs 存储、日志记录、指标和路由器子域。  默认值不会安装任何这些选项，并将nip.io用作路由器子域进行测试。 启用 CNS 将安装另外三个计算节点，以及另外三个附加磁盘，这些磁盘将承载 glusterfs pod。  

输入输入参数的值，然后单击"**确定"**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 配置容器本机存储 （CNS） | 在 OpenShift 群集中安装 CNS 并将其启用为存储。 如果禁用 Azure 提供程序，则为默认值 |
| 配置群集日志记录 | 将 EFK 日志记录功能安装到群集中。  适当调整红外节点的大小，以承载 EFK pod |
| 配置群集的指标 | 将 Hawkular 指标安装到 OpenShift 群集中。  适当调整体形节点的大小，以承载 Hawkular 指标窗格 |
| 默认路由器子域 | 选择用于测试或自定义的 nipio 以输入您自己的子域进行生产 |
 
   ![提供其他刀片](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**附加设置 - 额外参数**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| （中新社）节点大小 | 接受默认节点大小或选择 **"更改大小"** 以选择新的 VM 大小 |
| 输入自定义子域 | 用于通过 OpenShift 群集上的路由器公开应用程序的自定义路由域。  请务必创建相应的通配符 DNS 条目* |
 
   ![提供额外的 cns 安装](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**摘要**

在此阶段进行验证，以检查核心配额足以部署为群集选择的 VM 总数。  查看输入的所有参数。  如果输入可以接受，请单击 **"确定"** 以继续。

   ![提供摘要边栏选项卡](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**买**

确认"购买"页面上的联系信息，然后单击 **"购买**"以接受 OpenShift 容器平台群集的使用条款并开始部署。

   ![提供购买刀片](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 使用**OpenShift 控制台 URL**使用浏览器连接到 OpenShift 控制台。 您也可以 SSH 到堡垒主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-container-platform-3x-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-container-platform-3x-troubleshooting.md)
- [使用 OpenShift 容器平台入门](https://docs.openshift.com)
- 