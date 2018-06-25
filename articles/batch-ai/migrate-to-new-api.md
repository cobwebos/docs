---
title: 迁移到更新后的 Azure Batch AI API | Microsoft Docs
description: 如何更新 Azure Batch AI 代码和脚本来使用工作区和试验资源
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: b59173259aa86a429b9f926a8e5ffbfd046451a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294871"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>迁移到更新后的 Batch AI API

在即将推出的 Batch AI REST API 版本 2018-05-01 和相关的 Batch AI SDK 和工具中，引入了重大更改和新功能。

如果你已在使用 Batch AI API 的以前版本，本文介绍了如何修改代码和脚本来使用新的 API。 请仅在新的 API 可用后进行这些更改。

## <a name="whats-changing"></a>有什么变化？

为响应客户反馈，我们删除了对作业数的限制并使得管理 Batch AI 资源更为轻松。 新的 API 引入了两个新资源：“工作区”和“试验”。 将在试验下收集相关的训练作业，并在工作区下组织所有相关的 Batch AI 资源（群集、文件服务器、试验、作业）。  

* **工作区** - 一种顶级集合，包含所有类型的 Batch AI 资源。 群集和文件服务器包含在工作区中。 工作区通常用来拆分属于不同组或项目的工作。 例如，你可以有一个开发工作区和一个测试工作区。 对于每个订阅，你可能仅需要有限数量的工作区。 

* **试验** - 可以一起查询和管理的相关作业的集合。 例如，使用试验将作为超参数优化整理的一部分执行的所有作业分组到一起。 

下图显示了一个示例资源层次结构。 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>监视和管理现有资源
在已在其中创建了 Batch AI 群集、作业或文件服务器的每个资源组中，Batch AI 服务都将创建一个名为 `migrated-<region>` 的工作区（例如 `migrated-eastus`）和一个名为 `migrated` 的试验。 若要访问以前创建的作业、群集或文件服务器，需要使用已迁移的工作区和试验。 

### <a name="portal"></a>门户 
若要使用门户访问以前创建的作业、群集或文件服务器，请首先选择 `migrated-<region>` 工作区。 选择工作区后，执行相关操作，例如调整大小或删除群集，以及查看作业状态和输出。 

### <a name="sdks"></a>SDK 
若要通过 Batch AI SDK 访问以前创建的作业、群集或文件服务器，请提供工作区名称和试验名称参数。 

如果使用的是 Python SDK，则下面的示例中显示了相关的更改。 在其他 Batch AI SDK 中，更改类似。 


#### <a name="get-old-cluster"></a>获取旧的群集 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>删除旧的群集 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>获取旧的文件服务器 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>删除旧的文件服务器  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>获取旧的作业 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>删除旧的作业

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
使用 Azure CLI 访问之前创建的作业、群集或文件服务器时，请使用 `-w` 和 `-e` 参数来提供工作区和试验名称。 


#### <a name="get-old-cluster"></a>获取旧的群集

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>删除旧的群集 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>获取旧的文件服务器

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>删除旧的文件服务器 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>获取旧的作业

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>删除旧的作业 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>创建 Batch AI 资源 
 
如果你在使用现有的某个 Batch AI SDK，则可以继续使用它来创建 Batch AI 资源（作业、群集或文件服务器），不需要更改代码。 但是，升级到新的 SDK 后，需要进行以下更改。
 
### <a name="create-workspace"></a>创建工作区 
根据你的方案，你可以通过门户或 CLI 一次性地手动创建工作区。 如果使用 CLI，请使用以下命令创建工作区： 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>创建试验 


根据你的方案，你可以通过门户或 CLI 一次性地手动创建试验。 如果使用 CLI，请使用以下命令创建试验： 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>创建群集、文件服务器和作业
 
如果使用门户来创建作业、群集或文件服务器，在创建试验期间，门户将引导你提供工作区名称和试验名称参数。

若要通过更新后的 SDK 创建作业、群集或文件服务器，请提供工作区名称参数。 如果使用的是 Python SDK，则下面的示例中显示了相关的更改。 将 *workspace_name* 和 *experiment_name* 替换为你之前创建的工作区和试验。 在其他 Batch AI SDK 中，更改类似。 


#### <a name="create-cluster"></a>创建群集 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>创建文件服务器 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>创建作业 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>后续步骤

* 请参阅 Batch AI API 参考：[CLI](/cli/azure/batchai)、[.NET](/dotnet/api/overview/azure/batchai)、[Java](/java/api/overview/azure/batchai)、[Node.js](/javascript/api/overview/azure/batchai)、[Python](/python/api/overview/azure/batchai) 和 [REST](/rest/api/batchai)