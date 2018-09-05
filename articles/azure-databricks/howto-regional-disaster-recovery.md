---
title: Azure Databricks 的区域性灾难恢复
description: 本文介绍一种在 Azure Databricks 中执行灾难恢复的方法。
services: azure-databricks
author: jasonwhowell
ms.author: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2018
ms.openlocfilehash: 671e18346651a40d7f286e984117ce0c9ae62364
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125963"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Azure Databricks 群集的区域性灾难恢复

本文介绍可用于 Azure Databricks 群集的灾难恢复体系结构，以及实现该设计的步骤。

## <a name="azure-databricks-overview"></a>Azure Databricks 概述

Azure Databricks 是基于Apache Spark 的快速、简单、协作型分析服务。 使用大数据管道时，原始或结构化的数据将通过 Azure 数据工厂以批的形式引入 Azure，或者通过 Kafka、事件中心或 IoT 中心进行准实时的流式传输。 此数据将驻留在 Data Lake（长久存储）、Azure Blob 存储或 Azure Data Lake Storage 中。 在运行分析工作流的过程中，可以使用 Azure Databricks 从 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)、[Azure Data Lake Storage](../data-lake-store/index.md)、[Azure Cosmos DB](../cosmos-db/index.yml) 或 [Azure SQL 数据仓库](../sql-data-warehouse/index.md)等多个数据源读取数据，并使用 Spark 将数据转化为前所未有的见解。

![Databricks 管道](media/howto-regional-disaster-recovery/databricks-pipeline.png)

## <a name="azure-databricks-architecture"></a>Azure Databricks 体系结构

在较高层面上，当你通过 Azure 门户创建 Azure Databricks 工作区时，会在所选的 Azure 区域（例如“美国西部”），将某个[托管设备](../managed-applications/overview.md)部署为订阅中的 Azure 资源。 此设备部署在 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中，该网络具有[网络安全组](../virtual-network/manage-network-security-group.md)和订阅中的 Azure 存储帐户。 该虚拟网络为 Databricks 工作区提供外围级安全性，并受网络安全组的保护。 在工作区中，可以通过提供辅助角色和驱动程序 VM 类型与 Databricks 运行时版本来创建 Databricks 群集。 保存的数据可在存储帐户（可以是 Azure Blob 存储或 Azure Data Lake Store）中使用。 创建群集后，可以通过笔记本、REST API、ODBC/JDBC 终结点运行作业：只需将作业附加到特定的群集即可。

Databricks 控制平面管理并监视 Databricks 工作区环境。 任何管理操作（例如创建群集）将从控制平面发起。 所有元数据（例如计划的作业）存储在可通过异地复制实现容错的 Azure 数据库中。

![Databricks 体系结构](media/howto-regional-disaster-recovery/databricks-architecture.png)

此体系结构的优势之一是，用户可将 Azure Databricks 连接到其帐户中的任何存储资源。 另一个重要优点在于，计算 (Azure Databricks) 和存储可以互相独立地缩放。

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>如何创建区域性灾难恢复拓扑

在上面的体系结构说明中可以看到，包含 Azure Databricks 的大数据管道使用了许多组件：Azure 存储、Azure 数据库和其他数据源。 Azure Databricks 是大数据管道的计算层。 它是临时性的层，这意味着，尽管数据仍在 Azure 存储中提供，但计算层（Azure Databricks 群集）可以终止，因此，在无需计算资源时，就无需支付其费用。 计算 (Azure Databricks) 和存储源必须位于同一区域，避免作业遇到较高的延迟。  

若要创建自己的区域性灾难恢复拓扑，需满足以下要求：

   1. 在不同的 Azure 区域中预配多个 Azure Databricks 工作区。 例如，在“美国东部 2”区域中创建主要 Azure Databricks 工作区。 在另一个区域（例如“美国西部”）中创建辅助灾难恢复 Azure Databricks 工作区。

   2. 使用[异地冗余存储](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)。 与 Azure Databricks 关联的数据默认存储在 Azure 存储中。 Databricks 作业的结果也存储在 Azure Blob 存储中，因此，在终止群集后，处理的数据具有持久性，并保持高可用性。 由于存储和 Databricks 群集共置在一起，因此必须使用异地冗余存储，以便在主要区域不再可访问时，可在次要区域中访问数据。

   3. 创建次要区域后，必须迁移用户、用户文件夹、笔记本、群集配置、作业配置、库、存储、初始化脚本，并重新配置访问控制。 以下部分介绍了其他详细信息。

## <a name="detailed-migration-steps"></a>详细迁移步骤

1. **在计算机上设置 Databricks 命令行接口**

   本文演示的许多代码示例使用命令行接口来执行大多数自动化步骤，因为命令行接口是基于 Azure Databricks REST API 的易用包装器。

   在执行任何迁移步骤之前，请在台式机或打算使用的虚拟机上安装 databricks-cli。 有关详细信息，请参阅[安装 Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > 本文中提供的任何 python 脚本预期使用版本高于 2.7、低于 3.x 的 Python。

2. **配置两个配置文件。**

   为主要工作区配置一个配置文件，为辅助工作区配置另一个配置文件：

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   在每个后续步骤中，本文中的代码块使用相应的工作区命令在配置文件之间切换。 请确保创建的配置文件的名称可以代入每个代码块。

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   如果需要，可以手动在命令行中切换：

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **迁移 Azure Active Directory 用户**

   手动将相同的 Azure Active Directory 用户添加到主要工作区中的辅助工作区。

4. **迁移用户文件夹和笔记本**

   使用以下 python 代码迁移包含嵌套文件夹结构和每个用户的笔记本的沙盒用户环境。

   > [!NOTE]
   > 此步骤不会复制库，因为基础 API 不支持库。

   复制以下 python 脚本并将其保存到某个文件，然后在 Databricks 命令行中运行它。 例如，`python scriptname.py`。

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **迁移群集配置**

   迁移笔记本后，可以选择性地将群集配置迁移到新工作区。 这几乎是一个完全自动化的步骤，除非你要执行选择性的群集配置迁移而不是完整迁移，否则可以使用 databricks-cli 来完成。

   > [!NOTE]
   > 遗憾的是，由于没有创建群集配置的终结点，此脚本会尝试立即创建每个群集。 如果订阅中没有足够的核心，群集创建操作可能会失败。 只要成功转移配置，即可忽略这种失败。

   下面提供的脚本列显从旧群集 ID 到新群集 ID 的映射，稍后可对作业迁移（配置为使用现有群集的作业）使用该映射。

   复制以下 python 脚本并将其保存到某个文件，然后在 Databricks 命令行中运行它。 例如，`python scriptname.py`。

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **迁移作业配置**

   如果在上一步骤中迁移了群集配置，则可以选择将作业配置迁移到新工作区。 这几乎是一个完全自动化的步骤，除非你要执行选择性的作业配置迁移而不是执行所有作业的迁移，否则可以使用 databricks-cli 来完成。

   > [!NOTE]
   > 计划作业的配置还包含“计划”信息，因此，在迁移后，该作业默认会根据配置的时间开始工作。 因此，以下代码块在迁移过程中会删除所有计划信息（目的是避免针对新旧工作区重复运行）。 在准备好交接后，请配置此类作业的计划。

   作业配置需要新群集或现有群集的设置。 如果使用现有群集，则以下脚本/代码会尝试将旧群集 ID 替换为新群集 ID。

   复制以下 python 脚本并将其保存到某个文件。 将 `old_cluster_id` 和 `new_cluster_id` 的值替换为在上一步骤中执行的群集迁移的输出。 在 databricks-cli 命令行中运行该命令，例如 `python scriptname.py`。

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **迁移库**

   目前无法直接将库从一个工作区迁移到另一个工作区。 需要手动将这些库重新安装到新工作区中。 可以使用 [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples)（用于将自定义库上传到工作区）和[库 CLI](https://github.com/databricks/databricks-cli#libraries-cli) 的组合来自动完成此过程。

8. **迁移 Azure Blob 存储和 Azure Data Lake Store 装入点**

   使用基于笔记本的解决方案手动重新装载所有 [Azure Blob 存储](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)和 [Azure Data Lake Store（第 1 代）](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)装入点。 存储资源应已装载到主要工作区，必须在辅助工作区中重复该操作。 无法使用外部 API 进行装载。

9. **迁移群集初始化脚本**

   可以使用 [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) 将任何群集初始化脚本从旧工作区迁移到新工作区。 首先，将所需的脚本从 `dbfs:/dat abricks/init/..` 复制到本地台式机或虚拟机。 接下来，将这些脚本复制到新工作区中的相同路径。

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **手动重新配置和重新应用访问控制。**

   如果现有的主要工作区配置为使用高级层 (SKU)，则有可能你同时在使用[访问控制功能](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control)。

   如果确实使用了访问控制功能，请手动将访问控制重新应用到资源（笔记本、群集、作业、表）。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Azure Databricks 文档](https://docs.azuredatabricks.net/user-guide/index.html)。
