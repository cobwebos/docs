---
title: Azure Kubernetes 服务（AKS）上的 Hyperledger 结构联合会
description: 如何在 Azure Kubernetes Service 上部署和配置 Hyperledger Fabric 联合会网络
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 93f78f2c8bc32a2012e5635e0daec10b8c51d167
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901628"
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

- **Azure 密钥保管库**：将部署密钥保管库实例，以保存客户提供的构造 CA 凭据和根证书，用于模板部署重试时，这是处理模板的机制。
- **Azure 托管磁盘**： azure 托管磁盘适用于分类帐和对等节点世界状态数据库的持久存储。
- **公共 ip**：部署的 AKS 群集的公共 ip 终结点，用于与群集进行交互。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric 区块链网络安装程序

开始之前，需要一个能够支持部署多个虚拟机和标准存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

使用以下步骤设置 Hyperledger Fabric 区块链网络：

- [部署 orderer/对等组织](#deploy-the-ordererpeer-organization)
- [构建联合会](#build-the-consortium)
- [运行本机 HLF 操作](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>部署 orderer/对等组织

若要开始 HLF 网络组件部署，请导航到[Azure 门户](https://portal.azure.com)。 选择 "**创建资源 > 区块链** **" > 在 Azure Kubernetes 服务上搜索 "Hyperledger Fabric"** 。

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
    - **组织名称**：用于不同数据平面操作的结构组织的名称。
    - **构造网络组件**：根据要设置的区块链网络组件，选择 "排序服务" 或 "对等节点"。
    - **节点数**-下面是两种类型的节点：
        - 订购服务-选择为网络提供容错的节点数。 仅3、5和7是受支持的 orderer 节点计数。
        - 对等节点-可以根据要求选择1-10 节点。
    - **对等节点世界状态数据库**：在 LevelDB 和 CoucbDB 之间进行选择。 当用户在 "构造网络组件" 下拉栏中选择对等节点时，会显示此字段。
    - **Fabric 用户名**：输入用于构造 CA 身份验证的用户名。
    - **FABRIC ca 密码**：输入用于构造 ca 身份验证的密码。
    - **确认密码**：确认构造 CA 密码。
    - **证书**：如果想要使用自己的根证书来初始化 fabric ca，请选择 "为 Fabric Ca 上传根证书" 选项，否则默认情况下 Fabric ca 会创建自签名证书。
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

若要生成区块链协会，请在部署订购服务和对等节点后，按顺序执行以下步骤。 **构建你的网络**脚本（byn.sh），它可帮助你设置联合会、创建频道和安装 chaincode。

> [!NOTE]
> 所提供的构建网络（byn）脚本严格用于演示/开发测试方案。 对于生产评分设置，建议使用本机 HLF Api。

运行 byn 脚本的所有命令都可以通过 Azure Bash 命令行接口（CLI）来执行。 可以通过登录到 Azure shell web 版本 ![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 选项位于 Azure 门户的右上角。 在命令提示符下，键入 bash，然后输入切换到 bash CLI。

有关详细信息，请参阅[Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) 。

![Azure Kubernetes 服务模板上的 Hyperledger 结构](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下载 byn.sh 和 yaml 文件。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**在 Azure CLI Bash shell 上设置以下环境变量**：

设置频道信息和 orderer 组织信息

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=
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

创建一个 Azure 文件共享，以便在对等组织和 orderer 组织之间共享各种公共证书。

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

请参阅 orderer 组织 AKS 群集和发出命令以创建新通道

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**联合会管理命令**

按照给定的顺序执行下面的命令，以在通道和联合会中添加对等组织。

1. 请在 Azure 文件存储上，中转到对等组织 AKS 群集并上传其成员服务提供（MSP）。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 请参阅 orderer 组织 AKS 群集并在渠道和联盟中添加对等组织。

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 返回到对等组织，并发出命令以联接通道中的对等节点。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

同样，若要在通道中添加更多对等组织，请根据所需的对等组织更新对等节点 AKS 环境变量，并执行步骤1到3。

**Chaincode 管理命令**

执行以下命令，以执行与 chaincode 相关的操作。 这些命令对演示 chaincode 执行所有操作。 此演示 chaincode 具有两个变量 "a" 和 "b"。 在实例化 chaincode 时，"a" 使用1000进行初始化，并使用2000初始化 "b"。 每次调用 chaincode 时，10个单位都将从 "a" 传输到 "b"。 对 chaincode 的查询操作显示了 "a" 变量的世界状态。

执行在对等组织 AKS 群集上执行的以下命令。

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Chaincode 操作命令**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>运行本机 HLF 操作

帮助客户开始在 AKS 上的 HLF 网络上执行 Hyperledger 本机命令。 提供的示例应用程序使用 fabric NodeJS SDK 来执行 HLF 操作。 提供这些命令是为了创建新的用户标识和安装自己的 chaincode。

### <a name="before-you-begin"></a>开始之前

对于应用程序的初始设置，请执行以下命令：

- 下载应用程序文件
- 生成连接配置文件和管理配置文件
- 导入管理员用户标识

完成初始设置后，可以使用 SDK 实现以下操作：

- 用户标识生成
- Chaincode 操作

上述命令可以从 Azure Cloud Shell 执行。

### <a name="download-application-files"></a>下载应用程序文件

第一次设置运行的应用程序是下载文件夹中的所有应用程序文件。

**创建应用文件夹并输入到文件夹中**：

```bash
mkdir app
cd app
```
执行以下命令以下载所有必需的文件和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
此命令需要时间来加载所有包。 成功执行命令后，可以在当前目录中看到 `node_modules` 文件夹。 所有必需的包都加载到 `node_modules` 文件夹中。

### <a name="generate-connection-profile-and-admin-profile"></a>生成连接配置文件和管理配置文件

在 `app` 文件夹中创建 `profile` 目录

```bash
cd app
mkdir ./profile
```
在 Azure cloud shell 上设置这些环境变量

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

它将在配置文件文件夹中创建 `<orgname>-ccp.json` 和 `<orgname>-admin.json` 名称分别为的组织的连接配置文件和管理员 `profile`。

同样，为每个 orderer 和对等组织生成连接配置文件和管理配置文件。


### <a name="import-admin-user-identity"></a>导入管理员用户标识

最后一步是将组织的管理员用户标识导入钱包。

```bash
npm run importAdmin -- -o <orgName>

```
上述命令执行 importAdmin 将管理员用户标识导入钱包。 此脚本从管理配置文件中读取管理员标识 `<orgname>-admin.json` 并将其导入钱包以执行 HLF 操作。

脚本使用文件系统钱包来存储标识。 它根据连接配置文件中 "钱包" 字段中指定的路径创建一个钱包。 默认情况下，使用 `<orgname>`初始化 "钱包" 字段，这意味着将在当前目录中创建一个名为 `<orgname>` 的文件夹来存储标识。 如果要在其他某个路径上创建钱包，请在连接配置文件中修改 "钱包" 字段，然后再运行 "注册管理员用户" 和其他任何 HLF 操作。

同样，导入每个组织的管理员用户标识。

有关命令中传递的参数的更多详细信息，请参阅命令帮助。

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>用户标识生成

按照给定的顺序执行以下命令，为 HLF 组织生成新的用户标识。

> [!NOTE]
> 在开始执行用户标识生成步骤之前，请确保已完成应用程序的初始设置。

在 azure cloud shell 上设置以下环境变量

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

注册并注册新用户

若要注册并注册新用户，请执行以下命令以执行 registerUser。 它在钱包中保存生成的用户标识。

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 管理员用户标识用于为新用户发出注册命令。 因此，在执行此命令之前，必须在钱包中具有管理员用户标识。 否则，此命令将失败。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode 操作


> [!NOTE]
> 从任何 chaincode 操作开始之前，请确保已完成应用程序的初始设置。

以下设置 chaincode Azure Cloud shell 上的特定环境变量：

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

可以执行以下 chaincode 操作：

- 安装 chaincode
- 实例化 chaincode
- 调用 chaincode
- 查询 chaincode

### <a name="install-chaincode"></a>安装 chaincode

执行以下命令，在对等组织上安装 chaincode。

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
它将在 `ORGNAME` 环境变量中的组织集的所有对等节点上安装 chaincode。 如果频道中有两个或多个对等组织，并且你想要在所有这些组织中安装 chaincode，请为每个对等组织单独执行这些命令。

请按照以下步骤操作：

- 将 `ORGNAME` 设置为 `<peerOrg1Name>` 并发出 `installCC` 命令。
- 将 `ORGNAME` 设置为 `<peerOrg2Name>` 并发出 `installCC` 命令。

  为每个对等组织执行。

有关命令中传递的参数的更多详细信息，请参阅命令帮助。

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>实例化 chaincode

执行以下命令以实例化对等方上的 chaincode。

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
在 `<instantiateFunc>` 和 `<instantiateFuncArgs>` 中分别传递实例化函数名称和逗号分隔的参数列表。 例如，在[fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)中，要实例化 chaincode 集 `<instantiateFunc>` 以 `"Init"` 并 `<instantiateFuncArgs>` 为空字符串 `""`。

> [!NOTE]
> 从通道中的任意一个对等组织执行命令一次。
> 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 因此，chaincode 在通道中的所有对等节点上实例化。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>调用 chaincode

执行以下命令以调用 chaincode 函数：

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
分别在 `<invokeFunction>` 和 `<invokeFuncArgs>` 中传递调用函数名称和逗号分隔的参数列表。 继续执行 fabcar chaincode 示例，调用 initLedger 函数 set `<invokeFunction>` `"initLedger"` 并 `<invokeFuncArgs>` 到 `""`。

> [!NOTE]
> 从通道中的任意一个对等组织执行命令一次。
> 成功将事务提交到 orderer 后，orderer 会将此事务分发给通道中的所有对等组织。 因此，会在通道中的所有对等组织的所有对等节点上更新世界状态。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>查询 chaincode

执行以下命令以查询 chaincode：

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

分别在 `<queryFunction>` 和 `<queryFuncArgs>` 中传递查询函数名称和逗号分隔的参数列表。 同样，将 `fabcar` chaincode 作为参考，查询世界状态集中的所有轿车 `<queryFunction>` `"queryAllCars"` 并 `<queryArgs>` 到 `""`。

有关命令中传递的参数的更多详细信息，请参阅命令帮助

```bash
npm run queryCC -- -h

```
