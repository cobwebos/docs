---
title: 在门户中创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 门户中创建、查看和删除 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d2885c6cc259cba74ab991ecf5046856984824f1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631234"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 门户中创建并管理 Azure 机器学习工作区


本文将介绍如何在 Azure 门户中针对 [Azure 机器学习](overview-what-is-azure-ml.md)创建、查看和删除 [Azure 机器学习工作区****](concept-workspace.md)。  门户是开始使用工作区的最简单方法，但随着需求的变化或自动化要求的增加，还可以[使用 CLI](reference-azure-machine-learning-cli.md)、[使用 Python 代码](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)或[使用 VS Code 扩展](tutorial-setup-vscode-extension.md)创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区

必须有 Azure 订阅，才能创建工作区。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

1. 使用 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。 

1. 在 Azure 门户的左上角，选择“+ 创建资源”****。

      ![创建新资源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜索栏查找“机器学习”****。

1. 选择“机器学习”****。

1. 在“机器学习”窗格中，选择“创建”以开始**** ****。

1. 提供以下信息来配置新工作区：

   字段|说明 
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws****。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。 工作区名称不区分大小写。
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml****。 你需要 *参与者* 或 *所有者* 角色才能使用现有的资源组。  有关访问权限的详细信息，请参阅 [管理对 Azure 机器学习工作区的访问](how-to-assign-roles.md)。
   区域 | 选择最靠近用户和数据资源的 Azure 区域来创建工作区。
   工作区版本 | 选择“基本”**** 或“企业”****。  此工作区版本决定了可访问的功能和定价。 详细了解 [Azure 机器学习](overview-what-is-azure-ml.md)。 

    ![配置工作区](./media/how-to-manage-workspace/select-edition.png)

1. 完成工作区配置后，选择“查看 + 创建”****。 （可选）使用 " [网络](#networking) " 和 " [高级](#advanced) " 部分为工作区配置更多设置。

1. 查看设置并进行任何其他更改或更正。 如果对设置感到满意，请选择“创建”****。

   > [!Warning] 
   > 在云中创建工作区可能需要几分钟时间。

   完成创建后，会显示部署成功消息。 
 
 1. 若要查看新工作区，请选择“转到资源”****。

### <a name="networking"></a>网络  

> [!IMPORTANT]  
> 有关将专用终结点和虚拟网络与工作区结合使用的详细信息，请参阅 [网络隔离和隐私](how-to-enable-virtual-network.md)。
    
1. 默认的网络配置是使用公用 __终结点，该终结点__可在公共 internet 上访问。 若要将对工作区的访问权限限制到已创建的 Azure 虚拟网络，可以改为选择 " __专用终结点__ " 作为 __连接方法__，然后使用 " __+ 添加__ " 配置终结点。 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="专用终结点选择&quot;:::  

1. 在 &quot; __创建专用终结点__ &quot; 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 &quot; __与专用 DNS 区域集成__ &quot;，并使用 &quot; __专用 DNS 区域__ " 字段选择区域。 选择 __"确定"__ 以创建终结点。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="专用终结点选择&quot;:::  

1. 在 &quot; __创建专用终结点__ &quot; 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 &quot; __与专用 DNS 区域集成__ &quot;，并使用 &quot; __专用 DNS 区域__ " 链接__ 页，提供一个唯一的 __链接名称__，然后选择要添加的 __虚拟网络__ 。 选择 __"确定"__ 以添加网络链接。    
    >   
    > 有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](/azure/private-link/private-endpoint-dns)。   

### <a name="vulnerability-scanning"></a>漏洞扫描

Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 你应允许 Azure 安全中心扫描资源并遵循其建议。 有关详细信息，请参阅安全中心和[Azure Kubernetes Services 与安全中心集成](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)的[azure 容器注册表映像扫描](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)。

### <a name="advanced"></a>高级    

默认情况下，工作区的指标和元数据存储在 Microsoft 维护的 Azure Cosmos DB 实例中。 此数据使用 Microsoft 托管的密钥进行加密。  

若要限制 Microsoft 在你的工作区中收集的数据，请选择 " __高业务影响" 工作区__。 有关此设置的详细信息，请参阅 [静态加密](concept-enterprise-security.md#encryption-at-rest)。

> [!IMPORTANT]  
> 只有在创建工作区时，才能选择高业务影响。 在创建工作区后，不能更改此设置。   
如果使用的是 Azure 机器学习的 __企业__ 版，则可以提供自己的密钥。 这样做会创建在 Azure 订阅中存储指标和元数据的 Azure Cosmos DB 实例。 使用以下步骤来使用你自己的密钥：    

> [!IMPORTANT]  
> 在执行以下步骤之前，必须先执行以下操作：   
>   
> 1. 在标识和访问管理) 中为订阅的 "参与者" 权限授权 __机器学习应用__ (。  
> 1. 按照 [将客户托管的密钥配置](/azure/cosmos-db/how-to-setup-cmk) 为：   
>     * 注册 Azure Cosmos DB 提供程序   
>     * 创建和配置 Azure Key Vault 
>     * 生成密钥  
>   
>     您无需手动创建 Azure Cosmos DB 实例，而是在创建工作区时为您创建一个实例。 此 Azure Cosmos DB 实例将使用基于此模式的名称在单独的资源组中创建： `<your-workspace-resource-name>_<GUID>` 。   
>   
> 在创建工作区后，不能更改此设置。 如果删除工作区使用的 Azure Cosmos DB，则还必须删除正在使用该工作区的工作区。

1. 选择 " __客户管理的密钥__"，然后选择 __"单击以选择密钥__"。   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="专用终结点选择&quot;:::  

1. 在 &quot; __创建专用终结点__ &quot; 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 &quot; __与专用 DNS 区域集成__ &quot;，并使用 &quot; __专用 DNS 区域__ " 按钮使用此密钥。 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="专用终结点选择&quot;:::  

1. 在 &quot; __创建专用终结点__ &quot; 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 &quot; __与专用 DNS 区域集成__ &quot;，并使用 &quot; __专用 DNS 区域__ ":::

### <a name="download-a-configuration-file"></a>下载配置文件

1. 如果要创建[计算实例](tutorial-1st-experiment-sdk-setup.md#azure)，请跳过此步骤。

1. 如果计划使用引用此工作区的本地环境中的代码，请**** 从工作区的“概述”**** 部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建计算实例时，此文件会添加到 VM 上的正确目录中。
## <a name="find-a-workspace"></a><a name="view"></a>查找工作区

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在顶部搜索字段中，键入“机器学习”。****  

1. 选择“机器学习”****。

   ![搜索 Azure 机器学习工作区](./media/how-to-manage-workspace/find-workspaces.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择工作区即可显示其属性。

## <a name="delete-a-workspace"></a>创建工作区

在 [Azure 门户](https://portal.azure.com/)中，选择要删除的工作区顶部的“删除”****。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="专用终结点选择&quot;:::  

1. 在 &quot; __创建专用终结点__ &quot; 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 &quot; __与专用 DNS 区域集成__ &quot;，并使用 &quot; __专用 DNS 区域__ ":::

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>故障排除

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

学习整个教程，了解如何通过 Azure 机器学习使用工作区来生成、定型和部署模型。

> [!div class="nextstepaction"]
> [教程：训练模型](tutorial-train-models-with-aml.md)
