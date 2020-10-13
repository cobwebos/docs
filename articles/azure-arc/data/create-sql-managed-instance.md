---
title: 在 Azure Arc 上创建 Azure SQL 托管实例
description: 在 Azure Arc 上创建 Azure SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2a931b1a3c3f88af1abec4fd1810aae09c849c48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934834"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上创建 Azure SQL 托管实例

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>登录到 Azure Arc 数据控制器

如果尚未登录，请先登录到 Azure Arc 数据控制器，然后才能创建实例。

```console
azdata login
```

然后系统会提示输入用户名、密码和系统命名空间。  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>创建 Azure SQL 托管实例

若要查看可用的 create options forSQL 托管实例，请使用以下命令：
```console
azdata arc sql mi create --help
```

若要创建 SQL 托管实例，请使用以下命令：

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

例如：

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  名称长度必须少于13个字符，且符合 [DNS 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  指定内存分配和 vCore 分配时，请使用此公式来确保你的创建成功-对于每个 1 vCore，你需要至少 4 GB 的可用内存（在运行 SQL 托管实例的 Kubernetes 节点上）。
>
>  创建 SQL 实例时，如果在 Azure 中进行预配，则不要在名称中使用大写字母
>
>  列出 Kubernetes 群集中的可用存储类运行 `kubectl get storageclass` 


> [!NOTE]
> 如果要自动创建 SQL 实例并避免管理员密码的交互式提示，可以在 `AZDATA_USERNAME` `AZDATA_PASSWORD` 运行命令之前，将和环境变量设置为所需的用户名和密码 `azdata arc sql mi create` 。
> 
>  如果使用 AZDATA_USERNAME 创建数据控制器并在同一终端会话中 AZDATA_PASSWORD，则 AZDATA_USERNAME 和 AZDATA_PASSWORD 的值也将用于创建 SQL 托管实例。

> [!NOTE]
> 创建 Azure SQL 托管实例将不会在 Azure 中注册资源。 以下文章介绍了用于注册资源的步骤： 
> - [使用 Kibana 和 Grafana 查看日志和指标](monitor-grafana-kibana.md)
> - [将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>查看 Azure Arc 上的实例

若要查看实例，请使用以下命令：

```console
azdata arc sql mi list
```

输出应如下所示：

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

如果你使用的是 AKS 或 `kubeadm` OpenShift 等，则可以从此处复制外部 IP 和端口号，并使用你喜欢的工具连接到它，以便连接到 SQL server/AZURE SQL 实例，如 Azure Data Studio 或 SQL Server Management Studio。 但是，如果使用的是快速入门 VM，请参阅 [连接到支持 Azure Arc 的 SQL 托管实例](connect-managed-instance.md) 一文，了解特殊说明。


## <a name="next-steps"></a>后续步骤
- [连接到启用了 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)
- [向 Azure 注册实例，并上传有关实例的指标和日志](upload-metrics-and-logs-to-azure-monitor.md)
- [使用 Azure Data Studio 部署 Azure SQL 托管实例](create-sql-managed-instance-azure-data-studio.md)
