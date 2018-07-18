---
title: Azure Stack 以太坊区块链解决方案模板
description: 使用自定义解决方案模板来部署和配置 Azure Stack 上的联盟以太坊区块链网络
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 07/03/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 0e03b524834f528ddb7555a344fbebe720b4d9ff
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446961"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Azure Stack 以太坊区块链解决方案模板

以太坊解决方案模板设计，使其更轻松且快速地部署和配置具有最小的 Azure 和以太坊知识的多成员联盟以太坊区块链网络。

有少量的用户输入和通过 Azure Stack 租户门户的单击部署，每个成员可以预配其网络占用量。 每个成员的网络足迹包含的一组负载平衡事务节点与该应用程序或用户可以进行交互以提交事务、 一组记录的事务，到挖掘节点和网络虚拟设备 (NVA)。 后续连接步骤连接的 Nva 来创建完全配置的多成员区块链网络。

## <a name="prerequisites"></a>必备组件

下载以下[从 Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS 版本 16.04.201802220
* Windows Server 2016 
* 适用于 Linux 2.0 自定义脚本 
* 自定义脚本扩展 

在 Azure 上区块链方案的详细信息，请参阅[以太坊证明工作联盟解决方案模板](../blockchain-workbench/ethereum-deployment-guide.md)。

可以部署多个虚拟机支持的 Azure 订阅是必需的。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="deployment-architecture"></a>部署体系结构

此解决方案模板可以部署一个或多成员以太坊联盟网络。 在使用网络虚拟设备和连接资源的链拓扑中连接虚拟网络。 

## <a name="deployment-use-cases"></a>部署用例

该模板可以部署以太坊联盟领导和成员联接不同的方式，下面是我们已测试的：
- 在多节点 Azure Stack，使用 Azure AD 或 AD FS，部署潜在顾客并使用同一个订阅的成员或具有不同的订阅。
- （使用 Azure AD) 单节点 Azure Stack 上部署潜在顾客和成员使用相同的订阅。

### <a name="standalone-and-consortium-leader-deployment"></a>独立应用程序和联盟领导部署

联盟领导模板在网络中配置的第一个成员的占用空间。 

1. 下载[GitHub 中的领导者模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. 在 Azure Stack 管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在编辑在右侧窗格中，复制并粘贴领导模板之前下载的 JSON。
    
    ![编辑领导模板](media/azure-stack-ethereum/edit-leader-template.png)

5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。
    
    ![编辑领导模板参数](media/azure-stack-ethereum/edit-leader-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 作为基础用于命名的已部署的资源的字符串。 | 使用长度为 1 到 6 的字母数字字符 | eth
    AUTHTYPE | 对虚拟机进行身份验证的方法。 | 密码或 SSH 公钥 | 密码
    ADMINUSERNAME | 每个已部署的 VM 的管理员用户名 | 1 - 64 个字符 | gethadmin
    ADMINPASSWORD (身份验证类型 = 密码)| 部署的每个虚拟机的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但你可以在预配后更改密码。|12 - 72 个字符|
    ADMINSSHKEY (身份验证类型 = sshPublicKey) | 用于远程登录的安全 shell 密钥。 | |
    GENESISBLOCK | 表示自定义起源块的 JSON 字符串。  指定此参数的值是可选的。 | |
    ETHEREUMACCOUNTPSSWD | 用于保护以太坊帐户的管理员密码。 | |
    ETHEREUMACCOUNTPASSPHRASE | 用于生成与以太坊帐户相关联的私钥的通行短语。 | |
    ETHEREUMNETWORKID | 联盟网络 ID。 | 使用 5 和 999999999 之间的任何值 | 72
    CONSORTIUMMEMBERID | 与联盟网络的每个成员关联的 ID。   | 此 ID 应是网络中唯一的。 | 0
    NUMMININGNODES | 挖掘节点数。 | 之间 2 到 15。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 之间 1 和 5。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    BASEURL | 若要获取从具体于模板的基 URL。 | 使用默认值，除非你想要自定义部署的模板。 | 

7. 选择“确定”。
8. 在中**自定义部署**，指定**订阅**，**资源组**，以及**资源组位置**。
    
    ![领导部署参数](media/azure-stack-ethereum/leader-deployment-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 部署联盟网络的订阅 | | 使用订阅
    资源组 | 部署联盟网络的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，你可以查看部署摘要**Microsoft。模板**中的资源组的部署部分。 摘要包含可用于加入联盟成员的输出值。

若要验证前导符的部署，请浏览领导的管理网站。 可以在的输出部分中找到管理员站点地址**Microsoft.Template**部署。  

![领导部署摘要](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>加入联盟成员部署

1. 下载[GitHub 中的联盟成员模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. 在 Azure Stack 管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在编辑在右侧窗格中，复制并粘贴领导模板以前下载的 JSON。
5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 作为基础用于命名的已部署的资源的字符串。 | 使用长度为 1 到 6 的字母数字字符 | eth
    AUTHTYPE | 对虚拟机进行身份验证的方法。 | 密码或 SSH 公钥 | 密码
    ADMINUSERNAME | 每个已部署的 VM 的管理员用户名 | 1 - 64 个字符 | gethadmin
    ADMINPASSWORD (身份验证类型 = 密码)| 部署的每个虚拟机的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但你可以在预配后更改密码。|12 - 72 个字符|
    ADMINSSHKEY (身份验证类型 = sshPublicKey) | 用于远程登录的安全 shell 密钥。 | |
    CONSORTIUMMEMBERID | 与联盟网络的每个成员关联的 ID。   | 此 ID 应是网络中唯一的。 | 0
    NUMMININGNODES | 挖掘节点数。 | 之间 2 到 15。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 之间 1 和 5。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    CONSORTIUMDATA | 指向相关联盟配置数据提供的其他成员的部署 URL。 此值可以位于领导者的部署输出。 | |
    REMOTEMEMBERVNETADDRESSSPACE | 领导 NVA IP 地址。 此值可以位于领导者的部署输出。 | | 
    REMOTEMEMBERNVAPUBLICIP | 领导 NVA IP 地址。 此值可以位于领导者的部署输出。 | | 
    CONNECTIONSHAREDKEY | 要建立连接的联盟网络成员之间预先确定的机密。 | |
    BASEURL | 有关模板的基 URL。 | 使用默认值，除非你想要自定义部署的模板。 | 

7. 选择“确定”。
8. 在中**自定义部署**，指定**订阅**，**资源组**，以及**资源组位置**。

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 部署联盟网络的订阅 | | 使用订阅
    资源组 | 部署联盟网络的资源组。 | | MemberResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，你可以查看部署摘要**Microsoft.Template**中的资源组的部署部分。 摘要包含可用于连接联盟成员的输出值。

若要验证成员的部署，请浏览成员的管理网站。 Microsoft.Template 部署的输出部分中，可以找到管理员站点地址。

![成员部署摘要](media/azure-stack-ethereum/ethereum-node-status-2.png)

如图所示，是成员的节点状态**未运行**。 这是因为未建立成员和领导者之间的连接。 成员和领导者之间的连接是双向连接。 在部署成员时，模板自动创建的连接从成员为领导。 若要创建从领导到成员的连接，转到下一步。

### <a name="connect-member-and-leader"></a>连接成员和领导者

此模板的主讲人创建一个连接到远程的成员。 

1. 下载[从 GitHub 连接成员和领导者模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. 在 Azure Stack 管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在编辑在右侧窗格中，复制并粘贴领导模板以前下载的 JSON。
    
    ![编辑连接模板](media/azure-stack-ethereum/edit-connect-template.png)

5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。
    
    ![编辑连接模板参数](media/azure-stack-ethereum/edit-connect-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 前导符的名称前缀。 此值可以位于领导者的部署输出。  | 使用长度为 1 到 6 的字母数字字符 | |
    MEMBERROUTETABLENAME | 领导的路由表的名称。 此值可以位于领导者的部署输出。 |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 地址空间的成员。 此值可以位于成员的部署输出。 | |
    CONNECTIONSHAREDKEY | 要建立连接的联盟网络成员之间预先确定的机密。  | |
    REMOTEMEMBERNVAPUBLICIP | 成员的 NVA IP 地址。 此值可以位于成员的部署输出。 | |
    MEMBERNVAPRIVATEIP | 领导的专用 NVA IP 地址。 此值可以位于领导者的部署输出。 | |
    位置 | 在 Azure Stack 环境的位置。 | | local
    BASEURL | 有关模板的基 URL。 | 使用默认值，除非你想要自定义部署的模板。 | 

7. 选择“确定”。
8. 在中**自定义部署**，指定**订阅**，**资源组**，以及**资源组位置**。
    
    ![连接部署参数](media/azure-stack-ethereum/connect-deployment-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 领导的订阅。 | | 使用订阅
    资源组 | 领导的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署完成后，需要几分钟领导和成员，若要启动的通信。 若要验证部署，请刷新成员的管理网站。 应运行的成员节点的状态。 

![验证部署](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>后续步骤

- 若要了解有关以太坊和 Azure 的详细信息，请参阅[区块链技术和应用程序 |Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/)。
- 在 Azure 上区块链方案的详细信息，请参阅[以太坊证明工作联盟解决方案模板](../blockchain-workbench/ethereum-deployment-guide.md)。