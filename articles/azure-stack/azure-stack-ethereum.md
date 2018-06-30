---
title: Azure 堆栈 Ethereum 解决方案模板
description: 使用自定义解决方案模板来部署和配置 Azure 堆栈上的联合会 Ethereum 网络
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114723"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Azure 堆栈 Ethereum 解决方案模板

Ethereum 解决方案模板旨在使其更方便和快捷使用部署和配置多成员联合会 Ethereum 网络学到的 Azure 和 Ethereum 知识。

有少量的用户输入和通过 Azure 堆栈管理员门户单击部署，每个成员可以设置其网络占用。 每个成员的网络占用包含一组负载平衡事务节点与该应用程序或用户可以进行交互以提交事务、 一组对记录事务处理的挖掘节点和网络虚拟设备 (NVA)。 连接的后续连接步骤的 NVAs 创建完全配置的多成员 blockchain 网络。

## <a name="prerequisites"></a>必备组件

下载以下[来自应用商店](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS 版本 16.04.201802220
* Windows Server 2016 
* 适用于 Linux 2.0 的自定义脚本 
* 自定义脚本扩展 

在 Azure 上 blockchain 方案的详细信息，请参阅[Ethereum 证明工作联合会解决方案模板](../blockchain-workbench/ethereum-deployment-guide.md)。

可以支持部署多个虚拟机的 Azure 订阅是必需的。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="deployment-architecture"></a>部署体系结构

此解决方案模板可以部署一个或多成员 Ethereum 联合会网络。 虚拟网络连接在链拓扑中使用网络虚拟设备和连接资源。 

## <a name="deployment-use-cases"></a>部署使用案例

模板可以部署 Ethereum 联合会主管和成员联接各种不同的方式，下面是我们已测试的：
- 在多节点 Azure 堆栈，使用 Azure AD 或 AD FS 上, 部署主管和使用同一订阅的成员或使用不同的订阅。
- 在单节点 Azure 堆栈 （与 Azure AD) 上部署主管和使用同一订阅的成员。

### <a name="standalone-and-consortium-leader-deployment"></a>独立应用程序和联合会主管部署

联合会主管模板在网络中配置的第一个成员的占用空间。 

1. 下载[从 GitHub 的主管模板](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. 在 Azure 堆栈管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在右侧编辑窗格中，复制并粘贴主管模板以前下载的 JSON。
    
    ![编辑主管模板](media/azure-stack-ethereum/edit-leader-template.png)

5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。
    
    ![编辑主管模板参数](media/azure-stack-ethereum/edit-leader-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 作为基用于命名部署的资源的字符串。 | 使用长度为 1 到 6 的字母数字字符 | eth
    AUTHTYPE | 对虚拟机进行身份验证的方法。 | 密码或 SSH 公钥 | 密码
    ADMINUSERNAME | 每个已部署的虚拟机的管理员用户名 | 1 - 64 个字符 | gethadmin
    ADMINPASSWORD (身份验证类型 = 密码)| 部署的每个虚拟机的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但你可以在预配后更改密码。|12 - 72 个字符|
    ADMINSSHKEY (身份验证类型 = sshPublicKey) | 用于远程登录的安全 shell 密钥。 | |
    GENESISBLOCK | 表示自定义起源块的 JSON 字符串。 | |
    ETHEREUMACCOUNTPSSWD | 管理员密码用于保护 Ethereum 帐户。 | |
    ETHEREUMACCOUNTPASSPHRASE | 用于生成与 Ethereum 帐户关联的私钥的通行短语。 | |
    ETHEREUMNETWORKID | 联合会网络 ID。 | 使用介于 5 和 999999999 之间的任何值 | 72
    CONSORTIUMMEMBERID | 与联合会网络的每个成员关联的 ID。   | 此 ID 应为在网络中唯一。 | 0
    NUMMININGNODES | 挖掘节点数。 | 之间 2 和 15。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 之间 1 和 5。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    BASEURL | 若要获取的具体于模板的基 URL。 | 使用默认值，除非你想要自定义部署模板。 | 

7. 选择“确定”。
8. 在**自定义部署**，指定**订阅**，**资源组**，和**资源组位置**。
    
    ![领导部署参数](media/azure-stack-ethereum/leader-deployment-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 部署联盟网络的订阅 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，你可以查看摘要部署**Microsoft。模板**资源组的部署部分中。 摘要包含可用于将联合会成员加入的输出值。

若要验证领导的部署，请浏览领导的管理员站点。 你可以在的输出部分中找到管理员站点地址**Microsoft.Template**部署。  

![领导部署摘要](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>联接联合会成员部署

1. 下载[从 GitHub 联合会成员模板](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. 在 Azure 堆栈管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在右侧编辑窗格中，复制并粘贴主管模板以前下载的 JSON。
5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 作为基用于命名部署的资源的字符串。 | 使用长度为 1 到 6 的字母数字字符 | eth
    AUTHTYPE | 对虚拟机进行身份验证的方法。 | 密码或 SSH 公钥 | 密码
    ADMINUSERNAME | 每个已部署的虚拟机的管理员用户名 | 1 - 64 个字符 | gethadmin
    ADMINPASSWORD (身份验证类型 = 密码)| 部署的每个虚拟机的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但你可以在预配后更改密码。|12 - 72 个字符|
    ADMINSSHKEY (身份验证类型 = sshPublicKey) | 用于远程登录的安全 shell 密钥。 | |
    CONSORTIUMMEMBERID | 与联合会网络的每个成员关联的 ID。   | 此 ID 应为在网络中唯一。 | 0
    NUMMININGNODES | 挖掘节点数。 | 之间 2 和 15。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 之间 1 和 5。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    CONSORTIUMDATA | 指向另一个成员的部署提供的相关联合会配置数据的 URL。 此值可以位于领导的部署输出。 | |
    REMOTEMEMBERVNETADDRESSSPACE | 领导 NVA IP 地址。 此值可以位于领导的部署输出。 | | 
    REMOTEMEMBERNVAPUBLICIP | 领导 NVA IP 地址。 此值可以位于领导的部署输出。 | | 
    CONNECTIONSHAREDKEY | 要建立连接的联合会网络成员之间预先建立的机密。 | |
    BASEURL | 模板的的基 URL。 | 使用默认值，除非你想要自定义部署模板。 | 

7. 选择“确定”。
8. 在**自定义部署**，指定**订阅**，**资源组**，和**资源组位置**。

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 部署联盟网络的订阅 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | MemberResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，你可以查看摘要部署**Microsoft.Template**资源组的部署部分中。 摘要包含可以用于连接联合会成员的输出值。

若要验证成员的部署，请浏览成员的管理站点。 你可以 Microsoft.Template 部署的输出部分中找到管理员站点地址。

![成员部署摘要](media/azure-stack-ethereum/ethereum-node-status-2.png)

如图所示，成员的节点状态是**未运行**。 这是因为未建立成员和主管之间的连接。 成员和主管之间的连接是双向连接。 在部署成员时，模板会自动创建连接从成员到领导者。 若要创建从主管到成员的连接，转到下一步。

### <a name="connect-member-and-leader"></a>将成员和主管连接

此模板创建到远程成员由领头连接。 

1. 下载[从 GitHub 连接成员与领导模板](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. 在 Azure 堆栈管理门户中，选择**新建 > 模板部署**从自定义模板进行部署。
3. 选择**编辑模板**编辑新的自定义模板。
4. 在右侧编辑窗格中，复制并粘贴主管模板以前下载的 JSON。
    
    ![编辑连接模板](media/azure-stack-ethereum/edit-connect-template.png)

5. 选择“保存”。
6. 选择**编辑参数**并完成你的部署的模板参数。
    
    ![编辑连接模板参数](media/azure-stack-ethereum/edit-connect-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 前导符的名称前缀。 此值可以位于领导的部署输出。  | 使用长度为 1 到 6 的字母数字字符 | |
    MEMBERROUTETABLENAME | 前导符的路由表的名称。 此值可以位于领导的部署输出。 |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 地址空间的成员。 此值可以位于成员的部署输出。 | |
    CONNECTIONSHAREDKEY | 要建立连接的联合会网络成员之间预先建立的机密。  | |
    REMOTEMEMBERNVAPUBLICIP | 成员的 NVA IP 地址。 此值可以位于成员的部署输出。 | |
    MEMBERNVAPRIVATEIP | 前导符的专用 NVA IP 地址。 此值可以位于领导的部署输出。 | |
    位置 | Azure 堆栈环境的位置。 | | local
    BASEURL | 模板的的基 URL。 | 使用默认值，除非你想要自定义部署模板。 | 

7. 选择“确定”。
8. 在**自定义部署**，指定**订阅**，**资源组**，和**资源组位置**。
    
    ![连接部署参数](media/azure-stack-ethereum/connect-deployment-parameters.png)

    参数名称 | 说明 | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 前导符的订阅。 | | 消耗订阅
    资源组 | 前导符的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择**创建**。

部署完成后，需为主管和成员以启动通信的几分钟。 若要验证部署，刷新成员的管理员网站。 应运行的成员节点的状态。 

![验证部署](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Ethereum 和 Azure 的详细信息，请参阅[Blockchain 技术和应用程序 |Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/)。
- 在 Azure 上 blockchain 方案的详细信息，请参阅[Ethereum 证明工作联合会解决方案模板](../blockchain-workbench/ethereum-deployment-guide.md)。