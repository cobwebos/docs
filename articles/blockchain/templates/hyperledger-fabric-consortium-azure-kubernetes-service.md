---
title: Azure Kubernetes 服务（AKS）上的 Hyperledger 结构联合会
description: 如何在 Azure Kubernetes Service 上部署和配置 Hyperledger Fabric 联合会网络
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: da4ec99f1b9d73ab67a2312094feaa1a89aee394
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980213"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务（AKS）上的 Hyperledger 结构联合会

可以使用 Azure Kubernetes 服务（AKS）模板上的 Hyperledger Fabric （HLF）在 Azure 上部署和配置 Hyperledger Fabric 联合会网络。

阅读本文后，可以：

- 获取 Hyperledger Fabric 的工作知识和构成 Hyperledger Fabric 区块链网络构建基块的各种组件。
- 了解如何在 Azure Kubernetes Service 上为生产方案部署和配置 Hyperledger Fabric 联合会。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger 结构联合会体系结构

若要在 Azure 上构建 Hyperledger Fabric 网络，需要部署对等节点的订购服务和组织。 作为模板部署的一部分创建的不同基础组件如下：

- **Orderer 节点**：负责分类帐中的事务排序的节点。 有序节点与其他节点一起构成了 Hyperledger Fabric 网络的订购服务。

- **对等节点**：主要承载分类帐和智能协定的节点，这是网络的基本元素。

- **FABRIC ca**：构造 Ca 是 Hyperledger Fabric 的证书颁发机构（CA）。 构造 CA 允许您初始化和启动承载证书颁发机构的服务器进程。 它允许您管理标识和证书。 默认情况下，每个作为模板的一部分部署的 AKS 群集都具有结构 CA pod。

- **CouchDB 或 LevelDB**：对等节点的全球状态数据库可以存储在 LevelDB 或 CouchDB 中。 LevelDB 是在对等节点中嵌入的默认状态数据库，并将 chaincode 数据存储为简单的键值对，并仅支持键、键范围和复合键查询。 CouchDB 是可选的备用状态数据库，在 chaincode 数据值建模为 JSON 时支持丰富的查询。

部署中的模板会旋转订阅中的各种 Azure 资源。 部署的不同 Azure 资源包括：

- **AKS 群集**：按客户提供的输入参数配置的 Azure Kubernetes 群集。 AKS 群集为运行 Hyperledger Fabric 网络组件配置了各种 pod。 创建的不同 pod 包括：

  - **构造工具**：构造工具负责配置 Hyperledger Fabric 组件。
  - **Orderer/对等端**： HLF 网络的节点。
  - **代理**：一种 NGNIX 代理，客户端应用程序可以通过该 POD 与 AKS 群集进行交互。
  - **FABRIC ca**：运行构造 ca 的 pod。
- **PostgreSQL**：部署 PostgreSQL 的实例以维护构造 CA 标识。

- **Azure 密钥保管库**：将部署密钥保管库实例，以保存客户提供的构造 CA 凭据和根证书，用于模板部署重试时，处理模板的机制。
- **Azure 托管磁盘**： azure 托管磁盘适用于分类帐和对等节点世界状态数据库的持久存储。
- **公共 ip**：部署的 AKS 群集的公共 ip 终结点，用于与群集进行交互。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric 区块链网络安装程序

开始之前，需要一个能够支持部署多个虚拟机和标准存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

使用以下步骤设置 Hyperledger Fabric 区块链网络：

- [部署 orderer/对等组织](#deploy-the-ordererpeer-organization)
- [构建联合会](#build-the-consortium)

## <a name="deploy-the-ordererpeer-organization"></a>部署 orderer/对等组织

若要开始 HLF 网络组件部署，请导航到[Azure 门户](https://portal.azure.com)。 选择 "**创建资源 > 区块链** **" > 在 Azure Kubernetes 服务上搜索 "Hyperledger Fabric"**。

1. 选择 "**创建**" 以启动模板部署。 在**Azure Kubernetes Service 上创建 Hyperledger Fabric**会显示。

    ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. 在 "**基本**信息" 页中输入 "项目详细信息"。

    ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 输入以下详细信息：
    - **订阅**：选择要在其中部署 HLF 网络组件的订阅名称。
    - **资源组**：创建新的资源组或选择现有的空资源组，该资源组将保存作为模板的一部分部署的所有资源。
    - **区域**：选择要在其中部署 HLF 组件的 azure Kubernetes 群集的 azure 区域。 此模板在 AKS 可用的所有区域中都可用，请确保选择订阅未按虚拟机（VM）配额限制的区域。
    - **资源前缀**：用于命名部署的资源的前缀。 资源前缀的长度必须小于六个字符，并且字符组合必须同时包含数字和字母。
4. 选择 "**构造设置**" 选项卡以定义将部署的 HLF 网络组件。

    ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 输入以下详细信息：
    - **组织名称**：用于不同数据平面操作的结构组织的名称。 每个部署中的组织名称必须是唯一的。
    - **构造网络组件**：根据要设置的区块链网络组件，选择 "排序服务" 或 "对等节点"。
    - **节点数**-下面是两种类型的节点：
        - 订购服务-选择为网络提供容错的节点数。 仅3、5和7是受支持的 orderer 节点计数。
        - 对等节点-可以根据要求选择1-10 节点。
    - **对等节点世界状态数据库**：在 LevelDB 和 CoucbDB 之间进行选择。 当用户在 "构造网络组件" 下拉栏中选择对等节点时，会显示此字段。
    - **Fabric 用户名**：输入用于构造 CA 身份验证的用户名。
    - **FABRIC ca 密码**：输入用于构造 ca 身份验证的密码。
    - **确认密码**：确认构造 CA 密码。
    - **证书**：如果想要使用自己的根证书来初始化构造 ca，请选择 "为 Fabric Ca 上传根证书" 选项，否则，"fabric ca" 会创建自签名证书。
    - **根证书**：上传需要初始化构造 CA 的根证书（公钥）。 支持采用 pem 格式的证书，证书应在 UTC 时区内有效。
    - **根证书私钥**：上传根证书的私钥。 如果你有一个包含公钥和私钥的 pem 证书，也可以在此处上传。


6. 选择 " **AKS 群集设置**" 选项卡以定义 Azure Kubernetes 群集配置，它是将在其上设置构造网络组件的底层基础结构。

    ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 输入以下详细信息：
    - **Kubernetes 群集名称**：创建的 AKS 群集的名称。 根据提供的资源前缀预填充此字段，如有必要，可以更改。
    - **Kubernetes 版本**：将部署在群集上的 Kubernetes 版本。 根据在 "**基本**信息" 选项卡中选择的区域，可用的支持版本可能会更改。
    - **DNS 前缀**： AKS 群集的域名系统（DNS）名称前缀。 创建群集后管理容器时，你将使用 DNS 连接到 Kubernetes API。
    - **节点大小**： Kubernetes 节点的大小，可以从 Azure 上提供的 VM 库存单位（sku）列表中进行选择。 为了获得最佳性能，建议采用标准 DS3 v2。
    - **节点计数**：要在群集中部署的 Kubernetes 节点数。 建议将此节点数至少设置为等于或大于 Fabric 设置中指定的 HLF 节点数。
    - **服务主体客户端 id**：输入现有服务主体的客户端 id，或创建 AKS authentication 所需的新的。 请参阅[创建服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)的步骤。
    - **服务主体客户端机密**：输入服务主体客户端 ID 中提供的服务主体的客户端机密。
    - **确认客户端密码**：确认服务主体客户端密码中提供的客户端密码。
    - **启用容器监视**：选择启用 AKS 监视，使 AKS 日志推送到指定的 Log Analytics 工作区。
    - **Log Analytics 工作区**：如果启用了监视，将用创建的默认工作区填充 Log Analytics 工作区。

8. 提供所有上述详细信息后，请选择 "**查看和创建**" 选项卡。查看和创建会触发对提供的值的验证。
9. 验证通过后，可以选择 "**创建**"。
部署通常需要10-12 分钟的时间，具体取决于指定的 AKS 节点的大小和数量。
10. 成功部署后，将在右上角通过 Azure 通知通知你。
11. 选择 "**中转到资源组**"，检查作为模板部署的一部分创建的所有资源。 所有资源名称都以 "**基本**信息" 设置中提供的前缀开头。

## <a name="build-the-consortium"></a>构建联合会

若要生成区块链协会，请在部署订购服务和对等节点后，按顺序执行以下步骤。 Azure HLF script （azhlf），可帮助设置联合会、创建频道和 chaincode 操作。

> [!NOTE]
> 脚本中有一个更新，此更新用于通过 Azure HLF 脚本提供更多功能。 如果要引用旧脚本，[请参阅此处](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 此脚本与 Azure Kubernetes 服务模板版本2.0.0 和更高版本上的 Hyperledger 结构兼容。 若要检查部署的版本，请执行[故障排除](#troubleshoot)中的步骤。

> [!NOTE]
> 提供的 Azure HLF （azhlf）脚本仅用于帮助演示/开发测试方案。 此脚本创建的通道和联盟具有基本的 HLF 策略，可简化演示/开发测试方案。 对于生产设置，我们建议根据组织的符合性需求，使用本机 HLF Api 更新通道/联合会 HLF 策略。


运行 Azure HLF 脚本的所有命令都可以通过 Azure Bash 命令行执行。 接口（CLI）。 可以通过登录到 Azure shell web 版本  ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 选项位于 Azure 门户的右上角。 在命令提示符下，键入 bash，然后输入切换到 bash CLI。

有关详细信息，请参阅[Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) 。

![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下图显示了在 orderer 组织和对等组织之间建立协会的分步过程。 以下部分中捕获了用于执行这些步骤的详细命令。

![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

对于客户端应用程序的初始设置，请执行以下命令： 

1.  [下载客户端应用程序文件](#download-client-application-files)
2.  [设置环境变量](#setup-environment-variables)
3.  [导入组织连接配置文件、管理员用户和 MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

完成初始设置后，可以使用客户端应用程序实现以下操作：  

- [通道管理命令](#channel-management-commands)
- [联合会管理命令](#consortium-management-commands)
- [Chaincode 管理命令](#chaincode-management-commands)

### <a name="download-client-application-files"></a>下载客户端应用程序文件

第一次设置是下载客户端应用程序文件。 执行以下命令以下载所有必需的文件和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
这些命令将克隆公共 GitHub 存储库中的 Azure HLF 客户端应用程序代码，然后加载所有相关 npm 包。 成功执行命令后，可以在当前目录中看到 node_modules 文件夹。 所有必需的包都加载到 node_modules 文件夹中。


### <a name="setup-environment-variables"></a>设置环境变量

> [!NOTE]
> 所有环境变量均遵循 Azure 资源命名约定。


**为 orderer 组织客户端设置以下环境变量**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**为对等组织客户端设置以下环境变量**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> 根据你的联盟中的对等组织的数量，你可能需要重复执行对等命令并相应地设置环境变量。

**设置以下环境变量来设置 azure 存储帐户**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

按照以下步骤创建 Azure 存储帐户。 如果已创建 azure 存储帐户，请跳过以下步骤

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

按照以下步骤在 azure 存储帐户中创建文件共享。 如果已创建文件共享，请跳过以下步骤

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

按照以下步骤生成 Azure 文件共享连接字符串

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>导入组织连接配置文件、管理用户标识和 MSP

发出以下命令，从 Azure Kubernetes 群集提取组织的连接配置文件、管理员用户标识和 MSP，并将这些标识存储在客户端应用程序本地存储中，即 "azhlfTool/store" 目录。

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

### <a name="channel-management-commands"></a>通道管理命令

> [!NOTE]
> 在开始进行任何通道操作之前，请确保已完成客户端应用程序的初始设置。  

下面是两个通道管理命令：

1. [Create 信道命令](#create-channel-command)
2. [设置定位点对等节点命令](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Create 信道命令

从 orderer 组织客户端发出命令以创建新通道。 此命令将创建仅包含 orderer 组织的通道。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>设置定位点对等节点命令
在对等组织客户端中，发出以下命令为指定通道上的对等组织设置锚点。

>[!NOTE]
> 执行此命令之前，请确保使用 "联盟管理" 命令在通道中添加对等组织。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`要设置为定位点对等节点的以空格分隔的对等节点的列表。 例如，

  - 如果`<anchorPeersList>`只想将 peer1 节点设置为定位点对等节点，则设置为 "peer1"。
  - 如果`<anchorPeersList>`要将 peer1 和 peer3 节点设置为锚点对等，则设置为 "peer1" "peer3"。

### <a name="consortium-management-commands"></a>联合会管理命令

>[!NOTE]
> 在开始进行任何联盟操作之前，请确保已完成客户端应用程序的初始设置。  

按给定顺序执行以下命令，以在通道和联合会中添加对等组织
1.  从对等组织客户端，上载 azure 存储上的对等组织 MSP

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  从 orderer 组织客户端下载 azure 存储中的对等组织 MSP，然后发出命令以在通道/协会中添加对等组织。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  在 orderer 组织客户端上，将 orderer 连接配置文件上传到 azure 存储，使对等组织可以使用此连接配置文件连接到 orderer 节点

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  从对等组织客户端，从 azure 存储下载 orderer 连接配置文件，然后发出命令以在通道中添加对等节点

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同样，若要在通道中添加更多对等组织，请根据所需的对等组织更新对等环境变量，并执行步骤1到4。


### <a name="chaincode-management-commands"></a>Chaincode 管理命令

>[!NOTE]
> 从任何 chaincode 操作开始之前，请确保已完成客户端应用程序的初始设置。  

**设置以下 chaincode 特定环境变量**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

可以执行以下 chaincode 操作：  

- [安装 chaincode](#install-chaincode)  
- [实例化 chaincode](#instantiate-chaincode)  
- [调用 chaincode](#invoke-chaincode)
- [查询 chaincode](#query-chaincode)


### <a name="install-chaincode"></a>安装 chaincode  

执行以下命令，在对等组织上安装 chaincode。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
它将在 ORGNAME 环境变量中的对等组织集的所有对等节点上安装 chaincode。 如果频道中有两个或多个对等组织，并且你想要在所有这些组织中安装 chaincode，请为每个对等组织单独执行此命令。  

请按照以下步骤操作：  

1.  根据`ORGNAME` peerOrg1 `USER_IDENTITY`和发出`./azhlf chaincode install`命令设置和。  
2.  根据`ORGNAME` peerOrg2 `USER_IDENTITY`和发出`./azhlf chaincode install`命令设置和。  

### <a name="instantiate-chaincode"></a>实例化 chaincode  

在对等客户端应用程序中，执行以下命令以实例化通道上的 chaincode。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
分别传递和`<instantiateFunc>` `<instantiateFuncArgs>`中的实例化函数名称和空格分隔的参数列表。 例如，在 chaincode_example02 chaincode 中，将 chaincode 设置为`<instantiateFunc>` `init`，并`<instantiateFuncArgs>`将设置为 "a" "2000" "b" "1000"。

> [!NOTE]
> 从通道中的任意一个对等组织执行命令一次。 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 因此，chaincode 在通道中的所有对等节点上实例化。  


### <a name="invoke-chaincode"></a>调用 chaincode  

在对等组织客户端中，执行以下命令以调用 chaincode 函数：  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

分别 `<invokeFunction>` 在和 `<invokeFuncArgs>` 中传递调用函数名称和空格分隔的参数列表。 继续执行 chaincode_example02。 chaincode 示例，将调用操作设置为 `<invokeFunction>`  `invoke` ，并 `<invokeFuncArgs>` 将设置为 "a" "b" "10"。  

>[!NOTE]
> 从通道中的任意一个对等组织执行命令一次。 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 因此，会在通道中的所有对等组织的所有对等节点上更新世界状态。  


### <a name="query-chaincode"></a>查询 chaincode  

执行以下命令以查询 chaincode：  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
分别 `<queryFunction>` 在和 `<queryFuncArgs>` 中传递查询函数名称和空格分隔的参数列表。 同样，将 chaincode_example02 "chaincode" 作为参考，将 "世界" 状态中的 "a" 的查询 `<queryFunction>` 值 `query`设置 `<queryArgs>`为和 "a"。  

## <a name="troubleshoot"></a>疑难解答

**验证正在运行的模板版本**

运行以下命令以查找模板部署的版本。

按照部署了模板的资源组设置以下环境变量。

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
运行以下命令以打印模板版本
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```
