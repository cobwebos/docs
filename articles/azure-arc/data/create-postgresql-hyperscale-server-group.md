---
title: 在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组
description: 在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fb628df5151f9124d7b7f319ff109ffca030ee90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317338"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

本文档介绍在 Azure Arc 上创建 PostgreSQL 超大规模服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>入门
如果已熟悉下面的主题，则可以跳过此段落。
在继续创建之前，您可能需要阅读一些重要的主题：
- [启用了 Azure Arc 的数据服务概述](overview.md)
- [连接模式和要求](connectivity.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

如果你想要在不自行预配完整环境的情况下试用，请快速开始使用 azure Kubernetes Service 上的 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) (AKS) 、AWS 弹性 Kubernetes SERVICE (EKS) 、Google Cloud Kubernetes ENGINE (GKE) 或 Azure VM。


## <a name="login-to-the-azure-arc-data-controller"></a>登录到 Azure Arc 数据控制器

必须首先登录到 Azure Arc 数据控制器，然后才能创建实例。 如果已登录到数据控制器，则可以跳过此步骤。

```console
azdata login
```

然后系统会提示输入用户名、密码和系统命名空间。  

> 如果使用脚本创建数据控制器，则命名空间应为 **arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的初始和临时步骤

在转到下一步之前实现此步骤。 若要将 PostgreSQL 超大规模服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为将运行 PostgreSQL 超大规模服务器组的服务帐户授予必要的权限。  (SCC) 的安全上下文约束是在部署 Azure **_arc 数据控制器_** 时添加的。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**服务器组名称** 是将在下一步中创建的服务器组的名称。_
   
有关 OpenShift 中 Scc 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。
你现在可以实现下一步。

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>创建 Azure Database for PostgreSQL 超大规模服务器组

若要在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组，请使用以下命令：

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **还有其他可用的命令行参数。 通过运行查看选项的完整列表 `azdata arc postgres server create --help` 。**
> - 用于备份 (存储类- _-scb_) 默认为数据控制器的数据存储类（如果未提供）。
> - --Volume-* 参数所接受的单位是 Kubernetes 的资源数量， (整数后跟其中一个 SI 后缀 (T，G，M，K，m) 或它们的两个等效项 (Ti，Gi，Mi，Ki) # A5。
> - 名称长度必须小于或等于12个字符，并且符合 DNS 命名约定。
> - 系统将提示你输入 _postgres_ 标准管理用户的密码。  在运行 create 命令之前，可以通过设置 `AZDATA_PASSWORD` 会话环境变量来跳过交互式提示。
> - 如果使用 AZDATA_USERNAME 部署了数据控制器并在同一终端会话中 AZDATA_PASSWORD 会话环境变量，则 AZDATA_PASSWORD 的值也将用于部署 PostgreSQL 超大规模服务器组。 如果你想要使用另一个密码，则 (1) 更新 AZDATA_PASSWORD 或 (2 的值) 删除 AZDATA_PASSWORD 环境变量，或在创建服务器组时，提示删除其值以交互方式输入密码。
> - PostgreSQL 超大规模数据库引擎的默认管理员用户的名称为 _postgres_ ，此时无法对其进行更改。
> - 在 Azure 中创建 PostgreSQL 超大规模服务器组不会立即注册资源。 在将 [资源清单](upload-metrics-and-logs-to-azure-monitor.md)  或 [使用情况数据](view-billing-data-in-azure.md) 上传到 azure 的过程中，将在 azure 中创建资源，你将能够在 Azure 门户中查看资源。



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>列出在弧线设置中创建的 Azure Database for PostgreSQL 服务器组

若要查看 Azure Arc 上的 PostgreSQL 超大规模服务器组，请使用以下命令：

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>获取终结点以连接到 Azure Database for PostgreSQL 服务器组

若要查看 PostgreSQL 实例的终结点，请运行以下命令：

```console
azdata arc postgres endpoint list -n <server group name>
```
例如：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

你可以使用 PostgreSQL 实例终结点从你喜欢的工具连接到 PostgreSQL 超大规模服务器组：  [Azure Data Studio](https://aka.ms/getazuredatastudio)、 [Pgcli](https://www.pgcli.com/) psql、pgAdmin 等。

如果使用 Azure VM 进行测试，请按照以下说明进行操作：

## <a name="special-note-about-azure-virtual-machine-deployments"></a>有关 Azure 虚拟机部署的特别注意事项

使用 Azure 虚拟机时，终结点 IP 地址将不显示 _公共_ IP 地址。 若要查找公共 IP 地址，请使用以下命令：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然后，可以将公共 IP 地址与端口组合在一起，以进行连接。

你可能还需要通过网络安全网关 (NSG) 来公开 PostgreSQL 超大规模服务器组的端口。 若要允许流量通过 (NSG) ，你需要添加一个规则，你可以使用以下命令执行此操作：

若要设置规则，需要知道 NSG 的名称。 使用以下命令确定 NSG：

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

获得 NSG 的名称后，可以使用以下命令添加防火墙规则。 此处的示例值为端口30655创建 NSG 规则，并允许来自 **任何** 源 IP 地址的连接。  这不是最佳安全做法！  可以通过指定特定于客户端 IP 地址的源地址前缀值或涵盖团队或组织的 IP 地址的 IP 地址范围，来更好地锁定内容。

将下面的--destination 端口范围参数的值替换为你在上面的 "azdata arc postgres server list" 命令中获取的端口号。

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>连接 Azure Data Studio

打开 Azure Data Studio，然后连接到你的实例，其中包含上面的外部终结点 IP 地址和端口号，以及在创建实例时指定的密码。  如果 " *连接类型* " 下拉列表中没有 "PostgreSQL"，则可以通过在 "扩展" 选项卡中搜索 PostgreSQL 来安装 PostgreSQL 扩展。

> [!NOTE]
> 你将需要单击 "连接" 面板中的 "高级" 按钮以输入端口号。

请记住，如果你使用的是 Azure VM，你将需要 _公共_ IP 地址，该地址可通过以下命令访问：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>连接 psql

若要访问 PostgreSQL 超大规模服务器组，请传递从上面检索到的 PostgreSQL 超大规模服务器组的外部终结点：

你现在可以连接任一 psql：

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>后续步骤

- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分散到多个 PostgreSQL 超大规模节点并受益于 Azure Database for PostgreSQL 超大规模的所有功能。 :
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
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
