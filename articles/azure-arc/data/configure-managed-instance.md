---
title: 配置启用了 Azure Arc 的 SQL 托管实例
description: 配置启用了 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934797"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>配置启用了 Azure Arc 的 SQL 托管实例

本文介绍如何配置启用了 Azure Arc 的 SQL 托管实例。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>为启用了 Azure Arc 的 SQL 托管实例配置资源

### <a name="configure-using-azdata"></a>使用 azdata 进行配置

可以通过 CLI 编辑启用了 Azure Arc 的 SQL 托管实例的配置 `azdata` 。 运行以下命令以查看配置选项。 

```
azdata arc sql mi edit --help
```

下面的示例设置 cpu 内核和内存请求和限制。

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

若要查看对 SQL 托管实例所做的更改，可以使用以下命令查看配置 yaml 文件：

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>配置服务器选项

你可以在创建后为启用了 Azure Arc 的 SQL 托管实例配置服务器配置设置。 本文介绍如何配置设置，例如启用或禁用 mssql Agent，启用特定跟踪标志来解决方案。

若要更改任何设置，请执行以下步骤：

1. 创建包括目标设置的自定义 `mssql-custom.conf` 文件。 下面的示例启用了 SQL 代理并启用跟踪标志1204。

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. 将 `mssql-custom.conf` 文件复制到 `master-0` Pod 中的 `mssql-miaa` 容器中的 `/var/opt/mssql`。 将 `<namespaceName>` 替换为大数据群集名称。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. 重启 SQL Server 实例。  将 `<namespaceName>` 替换为大数据群集名称。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**已知的限制**
- 以上步骤需要 Kubernetes 群集管理员权限
- 这会在整个预览版中随时更改
