---
title: 在 Azure Kubernetes Service 上部署 Hyperledger 结构联合会
description: 如何在 Azure Kubernetes Service 上部署和配置 Hyperledger Fabric 联合会网络
ms.date: 08/06/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 081c7a10ee091f573e8f999c94588ef85c784f74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651558"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>在 Azure Kubernetes Service 上部署 Hyperledger 结构联合会

可以使用 Azure Kubernetes Service (AKS) 模板上的 Hyperledger Fabric 在 Azure 上部署和配置 Hyperledger Fabric 联合会网络。

阅读本文后，可以：

- 具有 Hyperledger 构造和构成 Hyperledger Fabric 区块链网络构建基块的组件的工作知识。
- 了解如何在 Azure Kubernetes Service 上为生产方案部署和配置 Hyperledger Fabric 联合会网络。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>选择 Azure 区块链解决方案

选择使用解决方案模板之前，请将方案与可用 Azure 区块链选项的常见用例进行比较：

选项 | 服务模型 | 常见用例
-------|---------------|-----------------
解决方案模板 | IaaS | 解决方案模板是 Azure 资源管理器模板，可用于预配完全配置的区块链网络拓扑。 模板为区块链网络类型部署和配置 Microsoft Azure 计算、网络和存储服务。 提供解决方案模板时没有服务级别协议。 使用 [Microsoft Q&支持页面](/answers/topics/azure-blockchain-workbench.html) 。
[Azure 区块链服务](../service/overview.md) | PaaS | Azure 区块链服务预览版简化了联合会区块链网络的构成、管理和监管。 使用 Azure 区块链服务提供需要 PaaS、联合会管理或合同和事务隐私的解决方案。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure 区块链工作台预览版是 Azure 服务和功能的集合，可帮助你创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 使用 Azure 区块链工作台将区块链解决方案或概念证明用于区块链应用程序。 提供 Azure 区块链工作台，无需提供服务级别协议。 使用 [Microsoft Q&支持页面](/answers/topics/azure-blockchain-workbench.html) 。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger 结构联合会体系结构

若要在 Azure 上构建 Hyperledger Fabric 网络，需要使用对等节点部署订购服务和组织。 通过使用 Azure Kubernetes 服务解决方案模板上的 Hyperledger Fabric，可以创建订单节点或对等节点。 需要为要创建的每个节点部署模板。

作为模板部署的一部分创建的基本组件如下：

- **Orderer 节点**：负责分类帐中的事务排序的节点。 有序节点与其他节点一起构成了 Hyperledger Fabric 网络的订购服务。

- **对等节点**：主要托管分类帐和智能协定的节点，它们是网络的基本元素。

- **FABRIC CA**：证书颁发机构 (CA) 用于 Hyperledger Fabric。 构造 CA 允许您初始化和启动承载证书颁发机构的服务器进程。 它允许您管理标识和证书。 默认情况下，每个作为模板的一部分部署的 AKS 群集都具有结构 CA pod。

- **CouchDB 或 LevelDB**：对等节点的世界状态数据库。 LevelDB 是在对等节点中嵌入的默认状态数据库。 它将 chaincode 数据存储为简单的键/值对，并仅支持键、键范围和复合键查询。 CouchDB 是可选的备用状态数据库，在 chaincode 数据值建模为 JSON 时支持丰富的查询。

部署中的模板会旋转订阅中的各种 Azure 资源。 部署的 Azure 资源是：

- **AKS 群集**：根据客户提供的输入参数配置的 Azure Kubernetes Service 群集。 AKS 群集为运行 Hyperledger Fabric 网络组件配置了各种 pod。 创建的 pod 包括：

  - **构造工具**：负责配置 Hyperledger 构造组件的工具。
  - **Orderer/对等端**： Hyperledger Fabric 网络的节点。
  - **代理**：一种 NGNIX 代理，客户端应用程序可以通过该 POD 与 AKS 群集进行通信。
  - **FABRIC ca**：运行构造 ca 的 pod。
- **PostgreSQL**：维护构造 CA 标识的数据库实例。

- **Key vault**：部署的、用于保存构造 CA 凭据和客户提供的根证书的 Azure Key Vault 服务的实例。 在模板部署重试时使用保管库来处理模板的机制。
- **托管磁盘**： Azure 托管磁盘服务的实例，它为分类帐和对等节点的环境状态数据库提供持久存储。
- **公共 IP**：部署用于与群集通信的 AKS 群集的终结点。

## <a name="deploy-the-orderer-and-peer-organization"></a>部署 orderer 和对等组织

开始之前，需要一个能够支持部署多个虚拟机和标准存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以 [创建免费的 azure 帐户](https://azure.microsoft.com/free/)。

若要开始部署 Hyperledger Fabric 网络组件，请转到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**创建资源**  >  " "**区块链**"，然后**在 "Azure Kubernetes 服务 (预览") 上搜索 "Hyperledger Fabric" **。

2. 在 " **基本** 信息" 选项卡上输入项目详细信息。

    ![显示 "基本信息" 选项卡的屏幕截图。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 输入以下详细信息：
    - **订阅**：选择要在其中部署 Hyperledger Fabric 网络组件的订阅名称。
    - **资源组**：创建新的资源组或选择现有的空资源组。 资源组将保存作为模板的一部分部署的所有资源。
    - **区域**：选择要在其中部署 Hyperledger Fabric 组件的 Azure Kubernetes 服务群集的 azure 区域。 此模板可用于 AKS 可用的所有区域。 选择你的订阅未按虚拟机 (VM) 配额限制的区域。
    - **资源前缀**：输入用于命名所部署资源的前缀。 它必须少于六个字符，并且字符组合必须同时包含数字和字母。
4. 选择 " **构造设置** " 选项卡以定义将部署的 Hyperledger Fabric 网络组件。

    ![显示 "构造设置" 选项卡的屏幕截图。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 输入以下详细信息：
    - **组织名称**：输入 Hyperledger Fabric 组织的名称，该名称是各种数据平面操作所必需的。 每个部署中的组织名称必须是唯一的。
    - **构造网络组件**：根据要设置的区块链网络组件，选择 " **排序服务** " 或 " **对等节点**"。
    - **节点数**：以下是两种类型的节点：
        - **订购服务**：选择用于提供网络容错能力的节点数。 支持的订单节点计数为3、5和7。
        - **对等节点**：可以根据需求选择1到10个节点。
    - **对等节点世界状态数据库**：在 LevelDB 和 CouchDB 之间进行选择。 当你在 "**构造网络组件**" 下拉列表中选择**对等节点**时，会显示此字段。
    - **FABRIC ca 用户名**：输入用于构造 ca 身份验证的用户名。
    - **FABRIC ca 密码**：输入用于构造 ca 身份验证的密码。
    - **确认密码**：确认构造 CA 密码。
    - **证书**：如果想要使用自己的根证书初始化构造 ca，请选择 " **为 Fabric ca 上传根证书** " 选项。 否则，结构 CA 默认创建自签名证书。
    - **根证书**：将根证书 (公钥) 上传到需要初始化 Fabric CA。 支持 pem 格式的证书。 证书应该有效并且在 UTC 时区中。
    - **根证书私钥**：上传根证书的私钥。 如果你有一个包含公钥和私钥的 pem 证书，也可以在此处上传该证书。


6. 选择 " **AKS 群集设置** " 选项卡以定义 Azure Kubernetes Service 群集配置，该配置是要在其上设置 Hyperledger 结构网络组件的底层基础结构。

    ![屏幕截图显示了一个 K S "群集设置" 选项卡。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 输入以下详细信息：
    - **Kubernetes 群集名称**：更改 AKS 群集的名称（如有必要）。 此字段根据提供的资源前缀预填充。
    - **Kubernetes 版本**：选择将部署在群集上的 Kubernetes 版本。 根据你在 " **基本** 信息" 选项卡上选择的区域，支持的可用版本可能会更改。
    - **Dns 前缀**：输入 AKS 群集的域名系统 (DNS) 名称前缀。 创建群集后，在管理容器时，你将使用 DNS 连接到 Kubernetes API。
    - **节点大小**：对于 Kubernetes 节点的大小，可以从 VM 库存量 (sku 列表中选择 Azure 上提供) 。 为了获得最佳性能，建议采用标准 DS3 v2。
    - **节点计数**：输入要在群集中部署的 Kubernetes 节点数。 建议将此节点计数保持为等于或大于 " **构造设置** " 选项卡上指定的 Hyperledger Fabric 节点数。
    - **服务主体客户端 id**：输入现有服务主体的客户端 id，或创建新的服务主体。 AKS authentication 需要服务主体。 请参阅 [创建服务主体的步骤](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)。
    - **服务主体客户端机密**：输入服务主体的客户端 ID 中提供的服务主体的客户端机密。
    - **确认客户端密码**：确认服务主体的客户端密码。
    - **启用容器监视**：选择启用 AKS 监视，使 AKS 日志推送到指定的 Log Analytics 工作区。
    - **Log Analytics 工作区**：如果启用了监视，将用创建的默认工作区填充 Log Analytics 工作区。

8. 选择 " **检查和创建** " 选项卡。此步骤将对您提供的值触发验证。
9. 通过验证后，选择“创建”。

    部署通常需要10到12分钟。 根据指定的 AKS 节点的大小和数目，时间可能会有所不同。
10. 成功部署后，将在右上角通过 Azure 通知通知你。
11. 选择 " **中转到资源组** "，检查作为模板部署的一部分创建的所有资源。 所有资源名称都以 " **基本** 信息" 选项卡上提供的前缀开头。

## <a name="build-the-consortium"></a>构建联合会

若要在部署订单服务和对等节点后生成区块链协会，请按顺序执行以下步骤。 Azure Hyperledger Fabric 脚本 (azhlf) 可帮助你设置联盟、创建通道，以及执行 chaincode 操作。

> [!NOTE]
> Azure Hyperledger Fabric (azhlf) 脚本已更新，以提供更多功能。 如果要参考旧脚本，请参阅 [GitHub 上的自述文件](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 此脚本与 Azure Kubernetes 服务模板版本2.0.0 和更高版本上的 Hyperledger 结构兼容。 若要检查部署的版本，请执行 [故障排除](#troubleshoot)中的步骤。

> [!NOTE]
> 此脚本仅用于帮助演示、开发和测试方案。 此脚本创建的通道和联盟具有基本的 Hyperledger 构造策略，可简化演示、开发和测试方案。 对于生产设置，我们建议根据组织的符合性需求，使用本机 Hyperledger 构造 Api 更新通道/联合会 Hyperledger 结构策略。


运行 Azure Hyperledger Fabric 脚本的所有命令都可以通过 Azure Bash 命令行界面 (CLI) 来执行。 可以通过 ![ Azure 门户右上角的 "Azure Kubernetes 服务模板上的 Hyperledger Fabric" 选项登录到 Azure Cloud Shell ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 。 在命令提示符下，键入 `bash` 并选择 Enter 键以切换到 BASH CLI，或从 Cloud Shell 工具栏中选择 **Bash** 。

有关详细信息，请参阅 [Azure Cloud Shell](../../cloud-shell/overview.md) 。

![显示 Azure Cloud Shell 中的命令的屏幕截图。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下图显示了在 orderer 组织与对等组织之间建立协会的分步过程。 以下部分显示了用于完成这些步骤的详细命令。

![构建联盟的过程关系图。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

完成初始设置后，请使用客户端应用程序实现以下操作：  

- 通道管理
- 联盟管理
- Chaincode 管理

### <a name="download-client-application-files"></a>下载客户端应用程序文件

第一次设置是下载客户端应用程序文件。 运行以下命令以下载所有必需的文件和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

这些命令将克隆公共 GitHub 存储库中的 Azure Hyperledger Fabric 客户端应用程序代码，然后加载所有相关 npm 包。 成功执行命令后，可以在当前目录中看到 node_modules 文件夹。 所有必需的包都加载到 node_modules 文件夹中。

### <a name="set-up-environment-variables"></a>设置环境变量

所有环境变量均遵循 Azure 资源命名约定。

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>为 orderer 组织的客户端设置环境变量

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>为对等组织的客户端设置环境变量

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

根据你的联盟中的对等组织数，你可能需要重复执行对等的命令并相应地设置环境变量。

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>为 Azure 存储帐户设置环境变量

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

使用以下命令创建 Azure 存储帐户。 如果已有 Azure 存储帐户，请跳过此步骤。

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

使用以下命令在 Azure 存储帐户中创建文件共享。 如果你已有一个文件共享，请跳过此步骤。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

使用以下命令生成 Azure 文件共享的连接字符串。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>导入组织连接配置文件、管理用户标识和 MSP

使用以下命令从 Azure Kubernetes Service 群集中获取组织的连接配置文件、管理员用户标识和托管服务提供程序 (MSP) ，并将这些标识存储在客户端应用程序的本地存储中。 本地存储的一个示例是 *azhlfTool/存储* 目录。

对于 orderer 组织：

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

对于对等组织：

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>创建频道

在 orderer 组织的客户端中，使用以下命令创建仅包含 orderer 组织的通道。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>添加联盟管理的对等组织

>[!NOTE]
> 在开始进行任何联盟操作之前，请确保已完成客户端应用程序的初始设置。  

按给定顺序运行以下命令，以在通道和联合会中添加对等组织： 

1.  在对等组织的客户端上，上载 Azure 存储上的对等组织的 MSP。

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  从 orderer 组织的客户端下载 Azure 存储中的对等组织的 MSP。 然后发出命令，将对等组织添加到通道和联盟。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  在 orderer 组织的客户端上，将 orderer 的连接配置文件上传到 Azure 存储，以便对等组织可以使用此连接配置文件连接到 orderer 节点。

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  从对等组织的客户端，从 Azure 存储下载 orderer 的连接配置文件。 然后，运行命令以在通道中添加对等节点。

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同样，若要在通道中添加更多对等组织，请根据所需的对等组织更新对等环境变量，然后重做步骤1到4。

### <a name="set-anchor-peers"></a>设置定位点对等

在对等组织的客户端中，运行命令为指定通道中的对等组织设置定位点对等方。

>[!NOTE]
> 在运行此命令之前，请确保使用 "联盟管理" 命令将对等组织添加到通道中。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` 要设置为定位点对等节点的以空格分隔的对等节点的列表。 例如：

  - `<anchorPeersList>` `"peer1"` 如果只想将 peer1 节点设置为定位点对等，则设置为。
  - `<anchorPeersList>` `"peer1" "peer3"` 如果要将 peer1 和 peer3 节点设置为定位点对等节点，则设置为。

## <a name="chaincode-management-commands"></a>Chaincode 管理命令

>[!NOTE]
> 在开始进行任何 chaincode 操作之前，请确保已完成客户端应用程序的初始设置。  

### <a name="set-the-chaincode-specific-environment-variables"></a>设置 chaincode 特定的环境变量

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>安装 chaincode  

运行以下命令，在对等组织上安装 chaincode。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
此命令会在环境变量中对等组织集的所有对等节点上安装 chaincode `ORGNAME` 。 如果两个或多个对等组织在你的频道中，并且你想要在所有它们上安装 chaincode，请为每个对等组织单独运行此命令。  

执行以下步骤:  

1.  `ORGNAME`根据设置并 `USER_IDENTITY` `peerOrg1` 运行 `./azhlf chaincode install` 命令。  
2.  `ORGNAME`根据设置并 `USER_IDENTITY` `peerOrg2` 运行 `./azhlf chaincode install` 命令。  

### <a name="instantiate-chaincode"></a>实例化 chaincode  

在对等客户端应用程序中，运行以下命令以实例化通道上的 chaincode。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

分别传递和中的实例化函数名称和以空格分隔的参数列表 `<instantiateFunc>` `<instantiateFuncArgs>` 。 例如，要实例化 chaincode_example02。请 chaincode，将 `<instantiateFunc>` 设置 `init` 为 `<instantiateFuncArgs>` 和 `"a" "2000" "b" "1000"` 。

还可以使用标志传递集合的配置 JSON 文件 `--collections-config` 。 或者， `-t` 在实例化用于专用事务的 chaincode 时使用标志设置暂时性参数。

例如：

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`部分是 JSON 文件的路径，该文件包含为私有数据 chaincode 的实例化定义的集合。 可在以下路径找到一个相对于 *azhlfTool* 目录的示例集合的配置 JSON 文件： `./samples/chaincode/src/private_marbles/collections_config.json` 。
`<transientArgs>`以字符串格式传递为有效的 JSON。 对任何特殊字符进行转义。 例如： `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> 从通道中的任意一个对等组织运行命令一次。 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 然后，将在通道中的所有对等组织上的所有对等节点上实例化 Chaincode。  

### <a name="invoke-chaincode"></a>调用 chaincode  

在对等组织的客户端上，运行以下命令以调用 chaincode 函数：  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

分别传递和中的调用函数名称和空格分隔的参数列表  `<invokeFunction>`    `<invokeFuncArgs>`   。 继续使用 chaincode_example02。 chaincode 示例，若要执行调用操作，请将设置  `<invokeFunction>`   为  `invoke`   和  `<invokeFuncArgs>`   `"a" "b" "10"` 。  

>[!NOTE]
> 从通道中的任意一个对等组织运行命令一次。 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 然后，在通道中的所有对等组织的所有对等节点上更新世界状态。  


### <a name="query-chaincode"></a>查询 chaincode  

运行以下命令以查询 chaincode：  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
认可对等方是安装了 chaincode 的对等方，为执行事务而调用。 必须设置 `<endorsingPeers>` 以包含当前对等组织中的对等节点名称。 列出给定 chaincode 的认可对等方和由空格分隔的通道组合。 例如： `-p "peer1" "peer3"`。

如果使用 *azhlfTool* 安装 chaincode，请将任何对等节点名称作为值传递到认可对等参数。 在该组织的每个对等节点上安装 Chaincode。 

分别在和中传递查询函数名称和空格分隔的参数列表  `<queryFunction>`    `<queryFuncArgs>`   。 再次获取 chaincode_example02。 chaincode 作为参考，若要查询世界状态的 "a" 的值，请将设置  `<queryFunction>`   为  `query` 和  `<queryArgs>` `"a"` 。  

## <a name="troubleshoot"></a>疑难解答

运行以下命令以查找模板部署的版本。

根据部署了模板的资源组设置环境变量。

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
运行以下命令以打印模板版本。

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>支持和反馈

若要及时了解 Azure 区块链工程团队提供的区块链服务产品和信息，请访问 [Azure 区块链博客](https://azure.microsoft.com/blog/topics/blockchain/)。

若要提供产品反馈或请求新功能，请通过[区块链的 Azure 反馈论坛](https://aka.ms/blockchainuservoice)来发布想法或进行投票。

### <a name="community-support"></a>社区支持

与 Microsoft 工程师和 Azure 区块链社区专家合作：

- [Microsoft Q&页面](/answers/topics/azure-blockchain-workbench.html) 
   
  针对区块链模板的工程支持仅限于部署问题。
- [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
