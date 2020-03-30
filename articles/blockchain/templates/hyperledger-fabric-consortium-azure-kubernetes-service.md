---
title: Azure 库伯奈斯服务 （AKS） 上的超分类帐结构联合体
description: 如何在 Azure 库伯奈斯服务上部署和配置超级分类帐结构联合网络
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476434"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure 库伯奈斯服务 （AKS） 上的超分类帐结构联合体

您可以使用 Azure 库伯奈斯服务 （AKS） 模板上的超级分类帐结构 （HLF） 在 Azure 上部署和配置超级分类帐结构联合网络。

阅读本文后，可以：

- 获得 Hyperledger Fabric 的工作知识以及构成 Hyperledger Fabric 区块链网络构建块的各种组件。
- 了解如何在 Azure Kubernetes 服务上部署和配置用于生产方案的超分类帐结构联合体。

## <a name="hyperledger-fabric-consortium-architecture"></a>超级分类帐结构联盟架构

要在 Azure 上构建 Hyperledger Fabric 网络，需要使用对等节点部署排序服务和组织。 作为模板部署的一部分创建的不同基本组件包括：

- **订购者节点**：负责分类帐中的事务排序的节点。 与其他节点一起，有序节点构成了 Hyperledger Fabric 网络的订购服务。

- **对等节点**：主要承载分类账和智能合约的节点，这些网络的基本元素。

- **结构 CA**： 结构 CA 是超级分类帐结构的证书颁发机构 （CA）。 Fabric CA 允许您初始化和启动承载证书颁发机构的服务器进程。 它允许您管理标识和证书。 默认情况下，作为模板的一部分部署的每个 AKS 群集都将具有结构 CA pod。

- **CouchDB 或 LevelDB**： 对等节点的世界状态数据库可以存储在 LevelDB 或 CouchDB 中。 LevelDB 是嵌入在对等节点中的默认状态数据库，将链码数据存储为简单的键值对，并且仅支持键、键范围和复合密钥查询。 CouchDB 是一个可选的备用状态数据库，当链码数据值建模为 JSON 时，支持丰富的查询。

部署上的模板会占用订阅中的各种 Azure 资源。 部署的不同 Azure 资源包括：

- **AKS 群集**：Azure Kubernetes 群集，根据客户提供的输入参数进行配置。 AKS 群集具有配置为运行 Hyperledger 结构网络组件的各种窗格。 创建的不同窗格包括：

  - **结构工具**：结构工具负责配置超分类元结构组件。
  - **订购器/对等窗格**：HLF 网络的节点。
  - **代理**：一个 NGNIX 代理窗格，客户端应用程序可以通过该窗格与 AKS 群集接口。
  - **结构 CA**： 运行结构 CA 的窗格。
- **PostgreSQL**： 部署 PostgreSQL 的实例以维护结构 CA 标识。

- **Azure 密钥保管库**：将部署密钥保管库实例以保存结构 CA 凭据和客户提供的根证书，这些证书用于模板部署重试，这是处理模板的机制。
- **Azure 托管磁盘**：Azure 托管磁盘用于分类帐和对等节点世界状态数据库的持久存储。
- **公共 IP**：为与群集接口而部署的 AKS 群集的公共 IP 终结点。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>超级账本织物区块链网络设置

开始之前，需要一个能够支持部署多个虚拟机和标准存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

使用以下步骤设置超账本结构区块链网络：

- [部署订购者/对等组织](#deploy-the-ordererpeer-organization)
- [建立联合体](#build-the-consortium)
- [运行本机 HLF 操作](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>部署订购者/对等组织

要开始使用 HLF 网络组件部署，导航到[Azure 门户](https://portal.azure.com)。 选择**创建一个资源>区块链**>搜索**超级分类账结构在Azure库伯奈斯服务**。

1. 选择 **"创建"** 以启动模板部署。 Azure**库伯奈斯服务上的"创建超级分类帐结构**"显示。

    ![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. 在 **"基础知识"** 页中输入项目详细信息。

    ![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 输入以下详细信息：
    - **订阅**：选择要部署 HLF 网络组件的订阅名称。
    - **资源组**：创建新资源组或选择现有的空资源组，资源组将保存所有资源作为模板的一部分部署。
    - **区域**：选择要为 HLF 组件部署 Azure 库伯奈斯群集的 Azure 区域。 该模板在 AKS 可用的所有区域都可用"确保选择订阅未达到虚拟机 （VM） 配额限制的区域。
    - **资源前缀**：用于命名已部署的资源的前缀。 资源前缀的长度必须少于六个字符，字符的组合必须同时包含数字和字母。
4. 选择 **"结构设置"** 选项卡以定义将部署的 HLF 网络组件。

    ![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 输入以下详细信息：
    - **组织名称**：结构组织的名称，这是各种数据平面操作所必需的。 组织名称需要每个部署是唯一的。 
    - **交换矩阵网络组件**：根据要设置的区块链网络组件选择订购服务或对等节点。
    - **节点数**- 以下是两种类型的节点：
        - 订购服务 - 选择节点数，为网络提供容错。 只有 3、5 和 7 是受支持的订购器节点计数。
        - 对等节点 - 您可以根据您的要求选择 1-10 个节点。
    - **对等节点世界状态数据库**：在 LevelDB 和库奇布DB 之间进行选择。 当用户在 Fabric 网络组件下拉下拉下下拉列表中选择对等节点时，将显示此字段。
    - **结构用户名**：输入用于结构 CA 身份验证的用户名。
    - **结构 CA 密码**：输入结构 CA 身份验证的密码。
    - **确认密码**：确认结构 CA 密码。
    - **证书**：如果要使用自己的根证书初始化 Fabric CA，请选择 Fabric CA 的上载根证书选项，否则默认情况下 Fabric CA 将创建自签名证书。
    - **根证书**：上载需要初始化结构 CA 的根证书（公钥）。 支持 .pem 格式的证书，证书应在 UTC 时区中有效。
    - **根证书私钥**：上载根证书的私钥。 如果您有 .pem 证书（包含公钥和私钥的组合），请在此处上载。


6. 选择**AKS 群集设置**选项卡以定义 Azure Kubernetes 群集配置，该配置是将设置 Fabric 网络组件的基础基础结构。

    ![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 输入以下详细信息：
    - **库伯内斯群集名称**：所创建的 AKS 群集的名称。 此字段根据提供的资源前缀进行预填充，如有必要，可以进行更改。
    - **库伯内特斯版本**：将在群集上部署的库伯内特的版本。 根据"**基本"** 选项卡中选择的区域，支持的版本可能会更改。
    - **DNS 前缀**：AKS 群集的域名系统 （DNS） 名称前缀。 创建群集后管理容器时，将使用 DNS 连接到 Kubernetes API。
    - **节点大小**：Kubernetes 节点的大小，您可以从 Azure 上可用的 VM 库存单位 （SKU） 列表中选择。 为了获得最佳性能，我们建议使用标准 DS3 v2。
    - **节点计数**：要在群集中部署的 Kubernetes 节点数的计数。 我们建议保持此节点计数至少等于或超过结构设置中指定的 HLF 节点数。
    - **服务主体客户端 ID**：输入现有服务主体的客户端 ID 或创建新的 AKS 身份验证所必需的。 请参阅，[创建服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)的步骤。
    - **服务主体客户端密钥**：输入服务主体客户端 ID 中提供的服务主体的客户端密钥。
    - **确认客户端机密**：确认服务主体客户端密钥中提供的客户端密钥。
    - **启用容器监视**：选择启用 AKS 监视，使 AKS 日志能够推送到指定的日志分析工作区。
    - **日志分析工作区**：日志分析工作区将填充启用监视时创建的默认工作区。

8. 提供上述所有详细信息后，选择 **"查看并创建**"选项卡。审核和创建将触发对所提供的值的验证。
9. 验证通过后，您可以选择**创建**。
部署通常需要 10-12 分钟，可能因指定的 AKS 节点的大小和数量而异。
10. 成功部署后，将通过右上角的 Azure 通知通知您。
11. 选择 **"转到资源组**"以检查作为模板部署的一部分创建的所有资源。 所有资源名称都将从 **"基本"** 设置中提供的前缀开始。

## <a name="build-the-consortium"></a>建立联合体

要构建区块链联盟在部署订购服务和对等节点后，您需要按顺序执行以下步骤。 **构建网络**脚本（byn.sh），它可以帮助您设置联合体、创建频道和安装链码。

> [!NOTE]
> 提供构建网络（byn）脚本严格用于演示/演示方案。 对于生产级设置，我们建议使用本机 HLF API。

运行 byn 脚本的所有命令都可以通过 Azure Bash 命令行接口 （CLI） 执行。 您可以通过 ![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 选项位于 Azure 门户的右上角。 在命令提示符上，键入 bash 并输入以切换到 bash CLI。

有关详细信息，请参阅[Azure 外壳](https://docs.microsoft.com/azure/cloud-shell/overview)。

![Azure 库伯奈斯服务模板上的超分类帐结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下载byn.sh和结构管理员.yaml 文件。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**在 Azure CLI Bash 外壳上设置以下环境变量**：

设置通道信息和订购器组织信息

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
设置对等组织信息

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

创建一个 Azure 文件共享，以便在对等方和订购器组织之间共享各种公共证书。

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**通道管理命令**

转到订购器组织 AKS 群集并发出命令以创建新通道

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**联盟管理命令**

按给定顺序执行以下命令，以便在通道和联合体中添加对等组织。

1. 转到对等组织 AKS 群集，并在 Azure 文件存储上载其成员服务提供 （MSP）。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 转到订购器组织 AKS 群集，并在通道和联合体中添加对等组织。

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 返回对等组织和发出命令以在通道中加入对等节点。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

同样，要在通道中添加更多对等组织，请根据所需的对等组织更新对等 AKS 环境变量，并执行步骤 1 到 3。

**链码管理命令**

执行以下命令以执行与链码相关的操作。 这些命令对演示链码执行所有操作。 此演示链码有两个变量"a"和"b"。 在链码实例化时，"a"用1000初始化，用2000年初始化"b"。 每次调用链码时，10 个单位从"a"转移到"b"。 链码上的查询操作显示"a"变量的世界状态。

执行对等组织 AKS 群集上执行的以下命令。

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**链码操作命令**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>运行本机 HLF 操作

帮助客户开始在 AKS 上的 HLF 网络上执行 Hyperledger 本机命令。 提供了使用结构 NodeJS SDK 执行 HLF 操作的示例应用程序。 提供这些命令是为了创建新的用户标识并安装您自己的链码。

### <a name="before-you-begin"></a>开始之前

按照以下命令进行应用程序的初始设置：

- 下载应用程序文件
- 生成连接配置文件和管理配置文件
- 导入管理员用户标识

完成初始设置后，可以使用 SDK 实现以下操作：

- 用户标识生成
- 链码操作

上述命令可以从 Azure 云外壳执行。

### <a name="download-application-files"></a>下载应用程序文件

运行应用程序的第一个设置是下载文件夹中的所有应用程序文件。

**创建应用文件夹并输入文件夹**：

```bash
mkdir app
cd app
```
执行以下命令以下载所有必需的文件和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
此命令需要时间来加载所有包。 成功执行命令后，可以看到当前目录中的`node_modules`文件夹。 所有必需的包都加载到文件夹中`node_modules`。

### <a name="generate-connection-profile-and-admin-profile"></a>生成连接配置文件和管理配置文件

在`profile`文件夹内`app`创建目录

```bash
cd app
mkdir ./profile
```
在 Azure 云外壳上设置这些环境变量

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

执行以下命令以生成组织的连接配置文件和管理配置文件

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

它将在配置文件文件夹中创建连接配置文件`profile`和组织管理员，分别具有名称`<orgname>-ccp.json`和`<orgname>-admin.json`名称。

同样，为每个订单人和对等组织生成连接配置文件和管理配置文件。


### <a name="import-admin-user-identity"></a>导入管理员用户标识

最后一步是在钱包中导入组织的管理员用户标识。

```bash
npm run importAdmin -- -o <orgName>

```
上述命令执行 importAdmin.js 以将管理员用户标识导入钱包。 该脚本从管理员配置文件`<orgname>-admin.json`读取管理员标识，并将其导入钱包以执行 HLF 操作。

脚本使用文件系统钱包来存储标识。 它根据连接配置文件中".wallet"字段中指定的路径创建钱包。 默认情况下，".wallet"字段使用`<orgname>`初始化，这意味着在当前目录中创建了名为 的`<orgname>`文件夹以存储标识。 如果要在其他路径上创建钱包，请修改连接配置文件中的".wallet"字段，然后再运行注册管理员用户和任何其他 HLF 操作。

同样，导入每个组织的管理员用户标识。

有关命令中传递的参数的更多详细信息，请参阅命令帮助。

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>用户标识生成

按给定顺序执行以下命令，以便为 HLF 组织生成新的用户标识。

> [!NOTE]
> 在开始使用用户标识生成步骤之前，请确保完成应用程序的初始设置。

在 Azure 云外壳上设置以下环境变量

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

注册并注册新用户

要注册和注册新用户，请执行以下命令，执行注册User.js。 它将生成的用户标识保存在钱包中。

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 管理员用户标识用于为新用户发出注册命令。 因此，在执行此命令之前，必须在钱包中具有管理员用户标识。 否则，此命令将失败。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>链码操作


> [!NOTE]
> 在开始任何链码操作之前，请确保完成应用程序的初始设置。

在 Azure 云外壳上设置以下链码特定环境变量：

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

可以执行以下链码操作：

- 安装链码
- 实例化链码
- 调用链码
- 查询链码

### <a name="install-chaincode"></a>安装链码

执行以下命令以在对等组织上安装链码。

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
它将在环境变量中`ORGNAME`设置的组织的所有对等节点上安装链码。 如果通道中有两个或多个对等组织，并且希望在所有这些组织上安装链码，请为每个对等组织分别执行这些命令。

按照以下步骤操作：

- 设置为`ORGNAME``<peerOrg1Name>`并发出`installCC`命令。
- 设置为`ORGNAME``<peerOrg2Name>`并发出`installCC`命令。

  为每个对等组织执行它。

有关命令中传递的参数的更多详细信息，请参阅命令帮助。

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>实例化链码

执行以下命令以实例化对等体上的链码。

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
传递实例化函数名称和逗号分隔的参数列表和`<instantiateFunc>``<instantiateFuncArgs>`分别。 例如，在[fabrcar 链码](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)中，实例化链码`<instantiateFunc>`设置为`"Init"`和`<instantiateFuncArgs>`空字符串`""`。

> [!NOTE]
> 从通道中的任何一个对等组织执行一次命令。
> 成功将事务提交到订购者后，订购者将此事务分发给通道中的所有对等组织。 因此，链码在通道中所有对等组织上的所有对等节点上实例化。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>调用链码

执行以下命令以调用链码函数：

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
传递调用函数名称和逗号分隔的参数列表，并`<invokeFunction>``<invokeFuncArgs>`分别引用。 继续 fabcar 链码示例，将 initLedger 函数`<invokeFunction>`设置为`"initLedger"``<invokeFuncArgs>`和`""`。

> [!NOTE]
> 从通道中的任何一个对等组织执行一次命令。
> 成功将事务提交到订购者后，订购者将此事务分发给通道中的所有对等组织。 因此，世界状态在通道中所有对等组织的所有对等节点上更新。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>查询链码

执行以下命令以查询链码：

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

传递查询函数名称和逗号分隔的参数列表，并`<queryFunction>``<queryFuncArgs>`分别传递。 再次，以`fabcar`链码作为参考，查询世界上`<queryFunction>`所有的汽车设置`"queryAllCars"`和。 `<queryArgs>` `""`

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run queryCC -- -h

```
