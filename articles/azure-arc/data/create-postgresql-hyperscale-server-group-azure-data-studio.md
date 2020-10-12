---
title: 使用 Azure Data Studio 创建启用了 Azure Arc 的 PostgreSQL 超大规模
description: 使用 Azure Data Studio 创建启用了 Azure Arc 的 PostgreSQL 超大规模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1b1cf90efa14345fa8395f5f62fd80934b922352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273001"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>使用 Azure Data Studio 创建启用了 Azure Arc 的 PostgreSQL 超大规模

本文档将指导你完成使用 Azure Data Studio 预配启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>连接到 Azure Arc 数据控制器

如果尚未登录，请先登录到 Azure Arc 数据控制器，然后才能创建实例。

```console
azdata login
```

然后，系统将提示你输入创建数据控制器的命名空间、用户名和密码以登录到控制器。

> 如果需要验证命名空间，则可以运行 ```kubectl get pods -A``` 来获取群集上所有命名空间的列表。

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的初始和临时步骤

在转到下一步之前实现此步骤。 若要将 PostgreSQL 超大规模服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为将运行 PostgreSQL 超大规模服务器组的服务帐户授予必要的权限。  (SCC) 的安全上下文约束是在部署 Azure **_arc 数据控制器_** 时添加的。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**服务器组名称** 是将在下一步中部署的服务器组的名称。_
   
有关 OpenShift 中 Scc 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。
你现在可以实现下一步。

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

1. 启动 Azure Data Studio
1. 在 "连接" 选项卡上，单击左上角的三个点，然后选择 "新建部署"
1. 从 "部署选项" 中，选择 **PostgreSQL 超大规模服务器组-Azure Arc**
    >[!NOTE]
    > 系统可能会提示你在 `azdata` 此处安装 CLI （如果当前未安装）。
1. 接受隐私和许可条款，并单击底部的 " **选择** "
1. 在 "部署 PostgreSQL 超大规模服务器组-Azure Arc" 边栏选项卡中，输入以下信息：
   - 输入服务器组的名称
   - 输入并确认服务器组的 _postgres_ 管理员用户的密码
   - 选择适用于数据的存储类
   - 选择适用于日志的存储类
   - 选择适用于备份的存储类
   - 选择要预配的辅助角色节点的数量
1. 单击 " **部署** " 按钮

这会开始在数据控制器上创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组。

几分钟后，创建应成功完成。

## <a name="next-steps"></a>后续步骤
- [使用 Azure Data Studio 管理服务器组](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [监视你的服务器组](monitor-grafana-kibana.md)
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以便跨多个 PostgreSQL 超大规模节点分发数据，并从 Azure Database for Postgres 超大规模的所有功能中获益。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 在上述文档中，跳过 **登录到 Azure 门户**的部分，& **创建 Azure Database for PostgreSQL (Citus) **。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

