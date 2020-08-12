---
title: 网络隔离和隐私
titleSuffix: Azure Machine Learning
description: 结合使用独立的 Azure 虚拟网络与 Azure 机器学习来保护试验/训练作业以及推理/评分作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121198"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>在专用终结点和虚拟网络的定型和推理期间进行网络隔离
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure 虚拟网络 (vnet) 中通过隔离 Azure 机器学习训练和推理作业来保护你的机器学习生命周期。 __虚拟网络__充当安全边界，可将 Azure 资源与公共 Internet 相隔离。 你也可以将 Azure 虚拟网络加入本地网络。 加入网络后，可以安全训练模型，以及访问用于推理的已部署模型。

可以使用__专用终结点__从虚拟网络访问 Azure 机器学习工作区。 专用终结点是虚拟网络中的一组专用 IP 地址，而对工作区的访问权限仅限于虚拟网络。 专用终结点有助于降低数据渗透的风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](/azure/private-link/private-link-overview)一文。

> [!NOTE]
> 使用 Mozilla Firefox 通过专用终结点访问工作区时，可能会遇到问题。 此问题可能与浏览器中的 "基于 HTTPS 的 DNS" 设置相关。 建议使用 Microsoft Edge 或 Google Chrome 解决此问题。

Azure 机器学习依赖于其他 Azure 服务的数据存储和计算资源 (用于) 定型和部署模型。 还可以在虚拟网络中创建这些资源。 例如，可以使用 Azure 机器学习计算来训练模型，然后将模型部署到 Azure Kubernetes 服务 (AKS)。 

## <a name="prerequisites"></a>先决条件

+ 一个 Azure 机器学习[工作区](how-to-manage-workspace.md)。

+ 对 [Azure 虚拟网络服务](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和 [IP 网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)具备一般性的实践知识。

+ 用于计算资源的预先存在的虚拟网络和子网。

+ 若要将资源部署到虚拟网络或子网中，你的用户帐户必须在 Azure 基于角色的访问控制 (RBAC) 中具有以下操作的权限：

    - 虚拟网络资源上的 "virtualNetworks/联接/操作"。
    - 子网资源上的 "virtualNetworks/子网/加入/操作"。

    有关 RBAC with 网络的详细信息，请参阅[联网内置角色](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>保护你的工作区

Azure 机器学习工作区可以有__公共终结点__或__专用终结点__。 公用终结点是可在公共 internet 上访问的一组 IP 地址，而专用终结点是虚拟网络中的一组专用 IP 地址。 

若要将对工作区的访问权限限制为仅通过专用 IP 地址发生，请使用专用终结点。

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 专用终结点不会影响 Azure 控制平面 (管理操作) 例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。
>
> 专用终结点禁止从虚拟网络外部访问工作区。

某些资源与专用终结点的组合需要 Enterprise edition 工作区。 若要了解哪些方案需要 Enterprise Edition，请参阅下表：

| 方案 | Enterprise</br>edition | 基本</br>edition |
| ----- |:-----:|:-----:| 
| 无虚拟网络或专用终结点 | ✔ | ✔ |
| 无专用终结点的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
| 无专用终结点的工作区。 具有专用终结点的其他资源 | ✔ | |
| 具有专用终结点的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
| 工作区和具有专用终结点的任何其他资源 | ✔ | |
| 具有专用终结点的工作区。 没有专用终结点或虚拟网络的其他资源 | ✔ | ✔ |
| 虚拟网络中的 Azure 容器注册表 | ✔ | |
| 工作区的客户托管密钥 | ✔ | |

> [!WARNING]
> 
> 在启用了专用终结点的工作区中不支持 Azure 机器学习计算实例。
>
> Azure 机器学习不支持使用启用了专用终结点的 Azure Kubernetes 服务。 相反，可以在虚拟网络中使用 Azure Kubernetes 服务。 有关详细信息，请参阅[在 Azure 虚拟网络中保护 Azure ML 试验和推理作业](how-to-enable-virtual-network.md)。

若要了解有关 Azure 中的专用终结点的详细信息，请参阅[Azure 专用链接](/azure/private-link/private-link-overview)一文。

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

可以通过使用 Azure 机器学习 SDK、CLI、Azure 资源管理器模板或 Azure 门户，使用专用终结点创建新的工作区。

__要求__

* 与专用终结点一起使用的虚拟网络必须禁用网络策略。 有关详细信息，请参阅[禁用专用终结点的网络策略](/azure/private-link/disable-private-endpoint-network-policy)。

__限制__

* 你无法通过公共 internet 连接到工作区，只能从虚拟网络内部连接。

* 如果使用专用终结点创建多个工作区，并且它们使用相同的专用 DNS 区域，则仅将第一个工作区添加到专用 DNS 区域的__虚拟网络链接__。

    若要解决此限制，请手动添加其他工作区的虚拟网络链接。 有关详细信息，请参阅[什么是虚拟网络链接](/azure/dns/private-dns-virtual-network-links)。

__配置__

有关如何使用虚拟网络和专用终结点以及其他配置选项创建工作区的信息，请参阅以下文章：

* [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。
* [在门户中创建工作区](how-to-manage-workspace.md)。
* [创建具有 Azure CLI 的工作区](how-to-manage-workspace-cli.md)。
* 若要使用 Python SDK，请参阅[PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py)和[Workspace。创建 ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-)参考文档。

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>机器学习工作室

__要求__

* 若要访问存储帐户中的数据，存储帐户必须与工作区位于同一虚拟网络中。

* 如果你的数据存储在虚拟网络中，则必须使用工作区[托管标识](../active-directory/managed-identities-azure-resources/overview.md)授予工作室对你的数据的访问权限。

    > [!IMPORTANT]
    > 尽管大多数 studio 都适用于虚拟网络中存储的数据，但集成笔记本不__会__。 集成笔记本不支持使用虚拟网络中的存储。 相反，可以使用来自计算实例的 Jupyter 笔记本。 有关详细信息，请参阅[计算群集 & 实例](#compute-instance)部分。

    如果不授予 studio 访问权限，你将收到此错误， `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 以下操作将不可用：

    * 预览工作室中的数据。
    * 在设计器中将数据可视化。
    * 提交 AutoML 试验。
    * 启动标记项目。

__限制__

* Azure 机器学习 studio 支持从虚拟网络中的以下数据存储类型读取数据：

    * Azure Blob
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL 数据库

__配置__

* __将数据存储配置为使用托管标识__访问数据。 这些步骤使用 Azure 基于资源的访问控制 (RBAC) 将工作区托管标识作为“读取者”添加到存储服务。 “读取者”访问权限允许工作区检索防火墙设置，并确保数据不会离开虚拟网络。

    1. 在工作室中，选择“数据存储”。

    1. 若要创建新的数据存储，请选择“+ 新建数据存储”。 若要更新现有的数据存储，请选择相应的数据存储并选择“更新凭据”。

    1. 在数据存储设置中，对于“允许 Azure 机器学习服务使用工作区托管标识来访问存储”，选择“是”。 

    > [!NOTE]
    > 这些更改可能需要长达 10 分钟才能生效。

* 对于__Azure Blob 存储__，还必须将工作区托管标识作为[Blob 数据读取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)添加，以便它能够从 blob 存储中读取数据。

* 默认情况下，设计器使用附加到工作区的存储帐户来存储输出。 不过，您可以指定它将输出存储到您有权访问的任何数据存储。 如果你的环境使用虚拟网络，则可以使用这些控件确保你的数据保持安全且可访问。 为管道设置新的默认存储：

    1. 在 "管道草稿" 中，选择管道标题附近的 "**设置" 齿轮图标**。
    1. 选择 "**选择默认数据存储**" 条目。
    1. 指定新的数据存储。

    还可以基于每个模块覆盖默认数据存储。 这使您可以控制每个模块的存储位置。

    1. 选择要指定其输出的模块。
    1. 展开 "**输出设置**" 部分。
    1. 选择 "**替代默认输出设置**"。
    1. 选择 "**设置输出设置**"。
    1. 指定新的数据存储。

* 如果使用__Azure Data Lake Storage Gen2__，则可以使用 RBAC 和 POSIX 样式的访问控制列表 (acl) 来控制虚拟网络内的数据访问。

    若要使用 RBAC，请将工作区托管标识添加到 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 有关详细信息，请参阅[基于角色的访问控制](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)。

    若要使用 ACL，可以向工作区托管标识分配访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

* __Azure Data Lake Storage Gen1__仅支持 POSIX 样式的访问控制列表。 可以像其他任何安全原则一样，将工作区托管标识访问权限分配给资源。 有关详细信息，请参阅[Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

* 若要使用托管标识访问存储在__AZURE Sql 数据库__中的数据，必须创建一个映射到托管标识的 SQL 包含的用户。 若要详细了解如何从外部提供程序创建用户，请参阅[创建映射到 Azure AD 标识的包含用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。

    创建 SQL 包含用户后，使用 [GRANT T-SQL 命令](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)向该用户授予权限。

* 如果要__从虚拟网络内部的资源访问工作室__ (例如，计算实例或虚拟机) ，则必须允许来自虚拟网络的出站流量发送到工作室。 

    例如，如果使用网络安全组 (NSG) 来限制出站流量，请将一条规则添加到__服务标记__目标 __AzureFrontDoor.Frontend__。

## <a name="use-datastores-and-datasets"></a>使用数据存储和数据集

> [!NOTE]
> 本部分介绍了 SDK 体验的数据存储和数据集用法。 有关工作室体验的详细信息，请参阅[机器学习工作室](#machine-learning-studio)部分。

__限制__

默认情况下，当你尝试使用 SDK 访问数据时，Azure 机器学习会执行数据有效性和凭据检查。 如果数据位于虚拟网络后面，则 Azure 机器学习无法访问数据，因此无法检查数据。 若要解决此问题，请在创建数据存储和数据集时跳过验证。

* 使用__数据存储__时：

    Azure Data Lake Store Gen1 和 Azure Data Lake Store Gen2 默认跳过验证，因此无需进一步操作。 但是，对于以下服务，你可以使用类似的语法来跳过数据存储验证：

    - Azure Blob 存储
    - Azure 文件共享
    - PostgreSQL
    - Azure SQL 数据库

    下面的代码示例创建一个新的 Azure Blob 数据存储并设置 `skip_validation=True`。

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* 使用__数据集__时：

    对于下列数据集类型，用于跳过数据集验证的语法是类似的：
    - 带分隔符的文件
    - JSON 
    - Parquet
    - SQL
    - 文件

    下面的代码创建一个新的 JSON 数据集并设置 `validate=False`。

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Azure 存储帐户

> [!IMPORTANT]
> 可以在虚拟网络中放置 Azure 机器学习的_默认存储帐户_和_非默认存储_帐户。

__要求__

* 存储帐户必须与用于训练或推理的计算实例或群集位于同一虚拟网络和子网中。

__配置__

若要保护工作区使用的 Azure 存储帐户，请在虚拟网络上为存储帐户启用__专用终结点__或__服务终结点__。

* 若要将存储帐户配置为使用__专用终结点__，请参阅[使用专用终结](/azure/storage/common/storage-private-endpoints.md)点一文。

* 若要将存储帐户配置为使用__服务终结点__，请执行以下步骤：

    1. 若要将存储帐户添加到工作区使用的虚拟网络，请使用[配置 Azure 存储防火墙和虚拟网络](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)一文中的 "__从虚拟网络授予访问权限__" 部分中的信息。
    1. 若要允许从虚拟网络上的 Microsoft 服务进行访问 (例如 Azure 机器学习) ，请使用[配置 Azure 存储防火墙和虚拟网络](/azure/storage/common/storage-network-security#exceptions)一文中的 "__例外__" 部分中的信息。
    1. 使用 Azure 机器学习 SDK 时，开发环境必须能够连接到 Azure 存储帐户。 当存储帐户位于虚拟网络中时，防火墙必须允许从开发环境的 IP 地址进行访问。 若要添加开发环境的 IP 地址，请使用[配置 Azure 存储防火墙和虚拟网络](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)一文中的 "__从 Internet IP 范围授予访问权限__" 部分中的信息。

## <a name="azure-container-registry"></a>Azure 容器注册表

__要求__

* Azure 机器学习工作区必须是 Enterprise Edition。 若要了解如何升级，请参阅[升级到 Enterprise Edition](how-to-manage-workspace.md#upgrade)。
* Azure 机器学习工作区区域应为[启用了专用链接的区域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)。 
* Azure 容器注册表必须是高级版。 若要详细了解如何升级，请参阅[更改 SKU](/azure/container-registry/container-registry-skus#changing-skus)。
* Azure 容器注册表必须与用于训练或推理的存储帐户和计算目标位于同一虚拟网络和子网中。
* Azure 机器学习工作区必须包含 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)。

__限制__

* 如果 ACR 位于虚拟网络后面，Azure 机器学习无法使用它来直接生成 Docker 映像。 而是使用计算群集来生成映像。

__配置__

1. 若要查找工作区的 Azure 容器注册表的名称，请使用以下方法之一：

    __Azure 门户__

    在工作区的“概述”部分中，“注册表”值链接到 Azure 容器注册表。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作区的 Azure 容器注册表" border="true":::

    __Azure CLI__

    如果[安装了用于 Azure CLI 的机器学习扩展](reference-azure-machine-learning-cli.md)，可以运行 `az ml workspace show` 命令来显示工作区信息。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令会返回类似于 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` 的值。 此字符串的最后一部分是工作区的 Azure 容器注册表的名称。

1. 若要限制对虚拟网络的访问，请按照[为注册表配置网络访问](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步骤操作。 添加虚拟网络时，为 Azure 机器学习资源选择虚拟网络和子网。

1. 使用 Azure 机器学习 Python SDK 将计算群集配置为生成 Docker 映像。 下面的代码片段展示了如何执行此操作：

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 存储帐户、计算群集和 Azure 容器注册表必须都位于虚拟网络的同一子网中。
    
    有关详细信息，请参阅 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 方法参考。

1. 若要使工作区与 ACR 实例通信，请应用以下 Azure 资源管理器模板：

    > [!WARNING]
    > 此模板启用工作区的专用终结点，并将其更改为企业工作区。 不能撤消这些更改。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>密钥保管库实例 

__要求__

__限制__

__配置__ 

若要在虚拟网络后使用 Azure Key Vault Azure 机器学习试验功能，请使用[配置 Azure Key Vault 防火墙和虚拟网络](/azure/key-vault/general/network-security)一文。

> [!IMPORTANT]
> 按照本文中的步骤操作时，请使用试验计算资源所使用的同一虚拟网络。 还必须__允许受信任的 Microsoft 服务跳过此防火墙__。

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>计算群集和实例 

__要求__

* 虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域中。
* 为计算实例或群集指定的子网必须具有足够的未分配 IP 地址，以容纳目标 VM 数目。 如果该子网没有足够的未分配 IP 地址，则只会为计算群集分配一部分资源。
* 如果你计划通过限制流量来保护虚拟网络，则对于计算服务，某些端口必须保持打开状态。
* 若要将多个计算实例或群集放入一个虚拟网络，可能需要请求提高一个或多个资源的配额。
* 如果工作区的一个或多个 Azure 存储帐户也在虚拟网络中受保护，它们必须与 Azure 机器学习计算实例或群集位于同一虚拟网络中。 
* 为了让计算实例 Jupyter 功能可以正常运行，请确保没有禁用 Web 套接字通信。

__限制__

* 机器学习计算实例或群集自动在包含虚拟网络的资源组中分配更多网络资源。 对于每个计算实例或群集，此服务分配以下资源：

    * 一个网络安全组
    * 一个公共 IP 地址
    * 一个负载均衡器
    
    对于__计算群集__，这些资源将 (删除，并在每次群集缩小到0个节点时重新创建) 。
    
    对于__计算实例__，在删除实例之前资源会保持到 (停止，而不会) 删除资源。

    这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)限制。

__配置__

* 若要创建机器学习计算群集，请按照以下步骤操作：

    1. 登录 [Azure 机器学习工作室](https://ml.azure.com/)，然后选择你的订阅和工作区。
    1. 选择左侧的“计算”。
    1. 在中心内选择“训练群集”，然后选择“+”。
    1. 在“新建训练群集”对话框中，展开“高级设置”部分。
    1. 若要将此计算资源配置为使用虚拟网络，请在“配置虚拟网络”部分中执行以下操作：

        1. 在“资源组”下拉列表中，选择包含虚拟网络的资源组。
        1. 在“虚拟网络”下拉列表中，选择包含子网的虚拟网络。
        1. 在“子网”下拉列表中，选择要使用的子网。

    ![机器学习计算的虚拟网络设置](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    也可以使用 Azure 机器学习 SDK 创建机器学习计算群集。 以下代码在名为 `mynetwork` 的虚拟网络的 `default` 子网中创建新的机器学习计算群集：

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    创建过程完成后，请在试验中使用该群集训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* 如果要在 Azure 计算实例上使用笔记本，则必须确保笔记本在与数据相同的虚拟网络和子网后的计算资源上运行。 

    在 "高级设置" 下的 "**高级设置**" 下，将计算实例配置为位于同一个虚拟网络中  >  **配置虚拟网络**。 无法将现有计算实例添加到虚拟网络中。

* 如果你计划通过限制进出公共 Internet 的网络流量来保护虚拟网络，则必须允许来自 Azure Batch 服务的入站通信。

    Batch 服务在附加到 VM 的网络接口 (NIC) 级别添加网络安全组 (NSG)。 这些 NSG 自动配置允许以下流量的入站和出站规则：

    - 端口 29876 和 29877 上的来自 __BatchNodeManagement__ 服务标记的入站 TCP 流量。

    - （可选）端口 22 上允许远程访问的入站 TCP 流量。 仅当要在公共 IP 上使用 SSH 进行连接时，才使用此端口。

    - 任何端口上通往虚拟网络的出站流量。

    - 任何端口上通往 Internet 的出站流量。

    - 对于端口 44224 上的来自 __AzureMachineLearning__ 服务标记的计算实例入站 TCP 流量。

    > [!IMPORTANT]
    > 在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信，则计算服务会将计算节点的状态设置为不可用。
    >
    > 不需要在子网级别指定 NSG，因为 Azure Batch 会配置自身的 NSG。 但是，如果包含 Azure 机器学习计算的子网具有关联的 NSG 或防火墙，则还必须允许前面列出的流量。

* 如果你不想要使用默认的出站规则，同时想要限制虚拟网络的出站访问，请执行以下步骤：

    1. 使用 NSG 规则来拒绝出站 Internet 连接。
    1. 对于__计算实例__或__计算群集__，请将出站流量限制为以下各项：
        - Azure 存储 - 使用__服务标记__ __Storage.RegionName__。 其中 `{RegionName}` 是 Azure 区域的名称。
        - Azure 容器注册表 - 使用__服务标记__ __AzureContainerRegistry.RegionName__。 其中 `{RegionName}` 是 Azure 区域的名称。
        - Azure 机器学习，通过使用服务标记 AzureMachineLearning
        - Azure 资源管理器，通过使用服务标记 AzureResourceManager
        - Azure Active Directory - 使用__服务标记__ __AzureActiveDirectory__

    > [!NOTE]
    > 如果你计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项，则还必须使用以下服务标记：
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > 当你的训练脚本中有类似于以下代码片段的代码时，需要此配置：
    >
    > __RunConfig 训练__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Estimator 训练__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* 如果要在 Azure 机器学习计算中使用[强制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)，则必须允许从包含计算资源的子网与公共 internet 通信。 此通信用于任务计划和访问 Azure 存储。

    可以通过两种方式来实现此目的：

    * 使用[虚拟网络 NAT](../virtual-network/nat-overview.md)。 NAT 网关为虚拟网络中的一个或多个子网提供出站 internet 连接。 有关信息，请参阅[设计具有 NAT 网关资源的虚拟网络](../virtual-network/nat-gateway-resource.md)。

    * 将[用户定义的路由 (udr) ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)添加到包含计算资源的子网。 为资源所在区域中的 Azure Batch 服务使用的每个 IP 地址建立一个 UDR。 借助这些 UDR，Batch 服务可以与计算节点进行通信，以便进行任务计划编制。 还要添加资源所在的 Azure 机器学习服务 IP 地址，因为这是访问计算实例所必需的。 若要获取 Batch 服务和 Azure 机器学习服务的 IP 地址列表，请使用以下方法之一：

        * 下载 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，并在文件中搜索 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`（其中 `<region>` 是你的 Azure 区域）。

        * 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 来下载信息。 下面的示例下载 IP 地址信息，并筛选掉美国东部 2 区域的信息：

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        添加 UDR 时，请为每个相关的 Batch IP 地址前缀定义路由，并将“下一跃点类型”设置为“Internet”。  

        除了定义的任何 Udr 外，还必须通过本地网络设备允许到 Azure 存储的出站流量。 具体而言，此流量的 Url 采用以下格式： `<account>.table.core.windows.net` 、 `<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 。 

        有关详细信息，请参阅[在虚拟网络中创建 Azure Batch 池](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

__要求__

* Azure Kubernetes 服务 (AKS) 实例，Azure 虚拟网络必须位于同一区域。 如果在虚拟网络中保护工作区使用的 Azure 存储帐户)  (，则这些帐户必须与 AKS 实例位于同一虚拟网络中。

* 若要__规划群集的 IP 寻址__，请遵循在[Azure Kubernetes Service 中配置高级网络 (AKS) ](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)一文中的先决条件。

* 若要限制到 AKS 群集的入站和出站通信，请按照[控制 Azure Kubernetes Service 中群集节点的出口流量](/azure/aks/limit-egress-traffic)一文中的指导进行操作，以确保正确配置来自 AKS 的_出站_通信。 在下面的配置部分中，将调用_传入_通信要求（如果有）。

__限制__

* 如果要使用启用了专用链接的 Azure Kubernetes 服务，则必须将其附加到工作区。 不能使用从 Azure 机器学习 (SDK、门户、CLI 等 ) 的私有链接创建 Azure Kubernetes 服务群集。

__配置__

> [!IMPORTANT]
> 本部分包含多个配置。 选择最适合你的需求的一个。

* 若要在__虚拟网络后使用 AKS，请使用公共负载均衡器__：

    1. 创建或附加 AKS 群集。 如果__创建__的是新群集，则必须指定要用于群集的虚拟网络。
    
        以下示例演示如何使用 Python SDK 创建新的 AKS 群集：

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        如果你有一个已位于虚拟网络后面的现有 AKS 群集，请使用[部署到 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的信息。

    1. 确保控制虚拟网络的网络安全组已为评分终结点启用了__入站__安全规则，以便可以从虚拟网络外部调用它。

* 若要在__虚拟网络后使用 AKS，请使用专用负载均衡器__：

    1. 创建或附加 AKS 群集。 如果__创建__的是新群集，则必须指定要用于群集的虚拟网络。
    
        以下示例演示如何使用 Python SDK 创建新的 AKS 群集：

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        如果你有一个已位于虚拟网络后面的现有 AKS 群集，请使用[部署到 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的信息。

    1. 若要查找 AKS 的服务主体或托管标识 ID，请使用以下 Azure CLI 命令。 将 `<aks-cluster-name>` 替换为群集的名称。 将替换 `<resource-group-name>` 为_包含 AKS 群集_的资源组的名称：

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        如果此命令返回的值 `msi` ，则使用以下命令来标识托管标识的主体 ID：

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. 若要查找包含虚拟网络的资源组的 ID，请使用以下命令。 将替换 `<resource-group-name>` 为_包含虚拟网络_的资源组的名称：

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. 若要将服务主体或托管标识作为网络参与者添加，请使用以下命令。 替换为 `<SP-or-managed-identity>` 服务主体或托管标识返回的 ID。 `<resource-group-id>`将替换为包含虚拟网络的资源组返回的 ID：

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. 若要更新 AKS 群集以使用__专用负载均衡器__，请使用 Python SDK。 以下代码段演示了如何更新已添加或附加到工作区的现有 AKS 群集：
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* __使用专用终结点附加 AKS__：

    1. 使用以下 Azure CLI 命令获取 AKS 群集将使用的子网的__子网 ID__ 。 例如，你的虚拟网络的默认子网：
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        此命令返回虚拟网络中子网的 Id 的数组。 以下 JSON 是仅具有一个子网的虚拟网络中的一个示例：

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        如果返回了多个 Id，请选择要使用的 Id。

    1. 若要创建 AKS 群集专用终结点，请使用[创建专用 Azure Kubernetes Service 群集](/azure/aks/private-clusters#advanced-networking)一文的 "__高级网络__" 部分中的信息。 创建群集时，请使用前一命令中包含参数的子网 ID `--vnet-subnet-id` 。

    1. 若要附加群集，请使用[部署到 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的信息。

    > [!TIP]
    > 使用此配置和限制 AKS 群集的出站通信时，无需允许端口__1194__或__9000__上的通信。 对于应允许的其他端口，请参阅[控制 Azure Kubernetes 服务中群集节点的出口流量](/azure/aks/limit-egress-traffic)一文中的指导。

## <a name="use-azure-container-instances-aci"></a>使用 Azure 容器实例 (ACI)

__要求__

* Azure 容器实例在部署模型时动态创建。 你必须为部署使用的子网启用子网委派，Azure 机器学习才能在虚拟网络中创建 ACI。

__限制__

* 虚拟网络必须与 Azure 机器学习工作区位于同一资源组中。

* 工作区的 Azure 容器注册表 (ACR) 也不能在虚拟网络中。

__配置__

若要将虚拟网络中的 ACI 用于工作区，请按照以下步骤操作：

1. 若要在虚拟网络上启用子网委派，请参阅[添加或删除子网委派](../virtual-network/manage-subnet-delegation.md)一文中的信息。 可以在创建虚拟网络时启用委派，也可以将它添加到现有网络。

    > [!IMPORTANT]
    > 启用委派时，使用 `Microsoft.ContainerInstance/containerGroups` 作为“将子网委派给服务”值。

2. 使用 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) 部署模型（使用 `vnet_name` 和 `subnet_name` 参数）。 将这些参数设置为启用了委派的虚拟网络名称和子网。

## <a name="azure-databricks"></a>Azure Databricks

__要求__

* 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域。
* 如果工作区的 Azure 存储帐户也在虚拟网络中受保护，则它们必须与 Azure Databricks 群集位于同一虚拟网络中。
* 除了 Azure Databricks 使用的 __databricks-private__ 和 __databricks-public__ 子网以外，还需要为虚拟网络创建 __default__ 子网。

__限制__

__配置__

若要详细了解如何结合使用 Azure Databricks 和虚拟网络，请参阅[在 Azure 虚拟网络中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虚拟机或 HDInsight 群集

__要求__

* Azure 机器学习只支持运行 Ubuntu 的虚拟机。
* 必须在虚拟机或 HDInsight 群集上启用 SSH 端口。

__限制__

__配置__

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，然后将群集置于 Azure 虚拟网络中。 有关详细信息，请参阅以下文章：

    * [为 Linux VM 创建和管理 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 系统会自动为基于 Linux 的 Azure 虚拟机创建 NSG。 此 NSG 允许从任何源访问端口22。 如果要限制对 SSH 端口的访问，则必须允许 Azure 机器学习的访问。 若要保留对 Azure ML 的访问，你必须__允许源服务使用____源服务标记__ __AzureMachineLearning__访问。 例如，以下 Azure CLI 命令将 SSH 规则修改为仅允许来自 Azure 机器学习的访问。

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    有关详细信息，请参阅适用于 Linux 虚拟机的 Azure 虚拟网络一文中的 "[创建网络安全组](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups)" 一节。
    
    保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 防火墙后面 Azure 机器学习工作区](how-to-access-azureml-behind-firewall.md)。
* [设置训练环境](how-to-set-up-training-targets.md)
* [设置专用终结点](how-to-configure-private-link.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
