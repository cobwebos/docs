---
title: Azure SQL Edge 使用情况和诊断数据配置
description: 了解如何在 Azure SQL Edge 中配置使用情况和诊断数据。
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 1f6624c454364ca19c8ce112cb1cbbef134f162d
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567885"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge 使用情况和诊断数据配置

默认情况下，Azure SQL Edge 收集有关其客户使用应用程序的方式的信息。 具体而言，Azure SQL Edge 收集有关部署经验、使用情况和性能的信息。 此信息有助于 Microsoft 改进产品以更好地满足客户需求。 例如，Microsoft 将收集有关客户遇到的错误代码的信息，以便我们可以解决相关 bug，改进关于如何使用 Azure SQL Edge 的文档，并确定是否应将功能添加到产品以更好地为客户提供服务。

具体而言，Microsoft 不会通过这种机制发送以下任何类型的信息：

- 用户表中的任何值。
- 任何登录凭据或其他身份验证信息。
- 任何个人或客户数据。

以下示例场景包括有助于改进产品的功能使用情况信息。

下面提供了用于使用情况和诊断数据收集的查询的示例查询。 此查询标识 Azure SQL Edge 中使用的不同流式处理数据源的计数和类型。 此数据可帮助 Microsoft 确定经常使用的流式处理数据源，以便 Microsoft 能够改善与这些数据源关联的性能和用户体验。 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type  
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job  
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es              
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id             
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js                
             on js.stream_id = es.object_id 
    ) ds            
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds        
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>禁用使用情况和诊断数据收集

可以使用以下方法之一禁用 Azure SQL Edge 上的使用情况和诊断数据收集。

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>使用环境变量禁用使用情况和诊断

若要在 Azure SQL Edge 上禁用使用情况和诊断数据收集，请添加以下环境变量并将其值设置为 `*False*` 。 有关使用环境变量配置 Azure SQL Edge 的详细信息，请参阅[使用环境变量配置](configure.md#configure-by-using-environment-variables)。

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE-启用收集使用情况和诊断数据。 这是默认配置。
- FALSE-禁用收集使用情况和诊断数据

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>使用 mssql. 会议文件禁用使用情况和诊断

若要在 Azure SQL Edge 上禁用使用情况和诊断数据收集，请在映射到 SQL Edge 模块中的/var/opt/mssql/文件夹的持久性存储驱动器上的 mssql. 驱动器文件中添加以下文件。 有关使用 mssql. 会议文件配置 Azure SQL Edge 的详细信息，请参阅[使用 mssql. 会议文件进行配置](configure.md#configure-by-using-an-mssqlconf-file)。

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>使用情况和诊断数据收集的本地审核

Azure SQL Edge 使用情况和诊断数据收集的本地审核组件可以将服务收集的数据写入指定的文件夹，表示将发送给 Microsoft 的数据 (日志) 。 本地审核的用途是使客户可以出于合规性、监管或隐私验证原因而查看 Microsoft 使用此功能收集的所有数据。

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>启用对使用情况和诊断数据的本地审核

在 Azure SQL Edge 上启用本地审核使用情况和诊断数据

1. 为新的本地审核日志存储创建目标目录。 此目标目录需要在映射到 SQL Edge 上的/var/opt/mssql/路径的同一装入卷中创建。

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. 使用环境变量或 mssql. 会议文件配置使用情况和诊断数据的审核。

   - 使用环境变量-将以下环境变量添加到 SQL Edge 部署。
   
     `*MSSQL_TELEMETRY_DIR = /var/opt/mssql/audit*`
   
   - 使用 mssql. 会议文件-在 mssql. 会议文件中添加以下行。
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = /var/opt/mssql/audit
       ```  

## <a name="next-steps"></a>后续步骤

- [连接到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
