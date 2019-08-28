---
title: 如何将 Azure Kubernetes 与 Azure Cosmos DB 配合使用
description: 了解如何在 Azure 上启动使用 Azure Cosmos DB（预览版）的 Kubernetes 群集
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093730"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>如何将 Azure Kubernetes 与 Azure Cosmos DB（预览版）配合使用

通过使用 Azure Cosmos DB 中的 etcd API，可将 Azure Cosmos DB 用作 Azure Kubernetes 的后端存储。 Azure Cosmos DB 实现 etcd 线路协议，从而允许主节点的 API 服务器使用 Azure Cosmos DB，就像访问一个本地安装的 etcd 一样。 Azure Cosmos DB 中的 etcd API 目前处于预览状态。 将 Azure Cosmos etcd API 用作 Kubernetes 的后备存储，可以获得以下优势： 

* 无需手动配置和管理 etcd。
* Cosmos 能保证 etcd 的高可用性（在单个区域中保持 99.99%，在多个区域中保持 99.999%）。
* etcd 灵活的可伸缩性。
* 默认保护且面向企业。
* 业界领先的复合型 SLA。

若要详细了解 Azure Cosmos DB 中的 etcd API，请参阅[概述](etcd-api-introduction.md)文章。 本文介绍如何使用 [Azure Kubernetes 引擎](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) 在 Azure 上启动使用 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) 而不是本地安装和本地配置的 etcd 的 Kubernetes 群集。 

## <a name="prerequisites"></a>先决条件

1. 安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 可以下载特定于自己的操作系统的 Azure CLI 并进行安装。

1. 安装[最新版本](https://github.com/Azure/aks-engine/releases)的 Azure Kubernetes 引擎。 [Azure Kubernetes 引擎](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine)页面中提供了不同操作系统的安装说明。 只需查看链接的文档中“安装 AKS 引擎”部分中的步骤。下载后，解压缩 zip 文件。

   Azure Kubernetes 引擎 (aks-engine) 可以生成 Azure 资源管理器模板，用于 Azure 上的 Kubernetes 群集。 aks-engine 的输入内容是群集定义文件，该文件描述所需的群集，包括业务流程协调程序、功能和代理。 输入文件的结构类似于 Azure Kubernetes 服务的公共 API。

1. Azure Cosmos DB 中的 etcd API 目前处于预览状态。 登录并使用此处的预览版： https://aka.ms/cosmosetcdapi-signup 。 提交表单后，订阅将被列入允许列表，以使用 Azure Cosmos etcd API。 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 部署群集

1. 打开命令提示符窗口，使用以下命令登录至 Azure：

   ```azurecli-interactive
   az login 
   ```

1. 如果有多个订阅，请切换到已列入 Azure Cosmos DB etcd API 允许列表的订阅。 可以使用以下命令切换至所需订阅：

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. 接下来，创建新的资源组，其中将部署 Azure Kubernetes 群集所需的资源。 请确保在“centralus”区域中创建资源组。 资源组并不一定要在“centralus”区域中，但目前只能在“centralus”区域中部署 Azure Cosmos etcd API。 所以最好让 Kubernetes 群集 和 Cosmos etcd 实例位于同一位置：

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. 接下来，创建 Azure Kubernetes 群集的服务主体，以便该群集能与属于同一资源组的资源进行通信。 可以使用 Azure CLI、PowerShell 或 Azure 门户创建服务主体，在本例中使用的是 CLI。

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   此命令输出服务主体的详细信息，例如：
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   请记下“appId”和“password”字段，因为将在后续步骤中使用到这些参数。 

1. 从命令提示符处导航至 Azure Kubernetes 引擎可执行文件所在的文件夹。 例如，可以在命令提示符上导航至该文件夹：

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. 打开所选的文本编辑器，并定义使用 Azure Cosmos DB etcd API 部署 Azure Kubernetes 群集的资源管理器模板。 将以下 JSON 定义复制到文本编辑器，并将其保存为 `apiModel.json`：

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   在 JSON/群集定义文件中，要记录的关键参数为 "cosmosEtcd": true。 该参数在“masterProfile”属性中，并指示部署过程使用 Azure Cosmos etcd API 而不是常规 etcd。 

1. 使用以下命令部署使用 Azure Cosmos DB 的 Azure Kubernetes 群集：

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes 引擎使用一个群集定义，该定义对所需的 Azure Kubernetes 形状、大小和配置进行概述。 有几项功能可通过群集定义启用。 在本示例中，将使用以下参数：

   * **subscription-id：** 启用了 Azure Cosmos DB etcd API 的 Azure 订阅 ID。
   * **client-id：** 服务主体的 appId。 `appId` 作为步骤 4 中的输出返回。
   * **Client-secret：** 服务主体的密码或随机生成的密码。 该值在步骤 4 中返回为“password”参数中的输出。 
   * **dnsPrefix：** 区域唯一的 DNS 名称。 该值会成为主机名的一部分，例如 myprod1 和 staging。
   * **location：** 应将群集部署到的位置，目前仅支持“centralus”。

   > [!Note]
   > 目前仅能在“centralus”区域中部署 Azure Cosmos etcd API。 
 
   * **api-model：** 指向模板文件的完全限定的路径。
   * **force-overwrite：** 此选项用于自动覆盖输出目录中的现有文件。
 
   以下命令展示的是一个示例部署：

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>验证部署

模板部署需要数分钟才能完成。 成功完成部署后，将在命令提示符中看到以下输出：

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

资源组现在包含的资源有虚拟机、Azure Cosmos 帐户 (etcd API)、虚拟网络、可用性集以及 Kubernetes 群集所需的其他资源。 

Azure Cosmos 帐户的名称将与指定的 DNS 前缀（带有 k8s）相匹配。 将自动使用名为 EtcdDB 的数据库以及名为 EtcdData 的容器预配 Azure Cosmos 帐户。 该容器存储所有与 etcd 相关的数据。 容器预配有一定数量的请求单位，你可以基于自己的工作负荷[缩放（增加/减少）吞吐量](scaling-throughput.md)。 

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure Cosmos 数据库、容器和项](databases-containers-items.md)
* 了解如何[优化预配吞吐量成本](optimize-cost-throughput.md)

