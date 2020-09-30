---
title: 排查 PostgreSQL 超大规模服务器组问题
description: 使用 Jupyter Notebook 对 PostgreSQL 超大规模服务器组进行故障排除
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8d1c9027b6a9a7b295ce83e26281832beca1bc33
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531949"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>PostgreSQL 超大规模服务器组故障排除
本文介绍可用于排查服务器组问题的一些方法。 除本文外，你可能还需要了解如何使用 [Kibana](monitor-grafana-kibana.md) 来搜索日志，或使用 [Grafana](monitor-grafana-kibana.md) 来可视化有关服务器组的指标。 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>获取有关执行 azdata 命令的详细信息
您可以向执行的任何 azdata 命令添加参数 **--debug** 。 这样做会向控制台显示有关执行该命令的其他信息。 您应该发现获取详细信息以帮助您了解该命令的行为非常有用。
例如，你可以运行
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

或
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

此外，你可以使用参数--有关任何 azdata 命令的帮助来显示特定命令的一些帮助信息列表。 例如：
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>收集数据控制器和服务器组的日志
阅读有关[为启用了 Azure Arc 的数据服务获取日志](troubleshooting-get-logs.md)的文章



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Azure Data Studio 中的 Jupyter 笔记本进行交互式故障排除
笔记本可以通过包括 Markdown 内容（用于说明要执行的操作内容/操作方法）来记录过程。 它还可以提供可执行代码来自动执行某个过程。  此模式适用于从标准操作过程到故障排除指南的一切。

例如，让我们对 PostgreSQL 超大规模服务器组进行故障排除，这可能会在使用 Azure Data Studio 时遇到一些问题。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>安装工具

`kubectl` `azdata` 在 Azure Data Studio 中安装 Azure Data Studio 和用于运行笔记本的客户端计算机上。 为此，请按照[安装客户端工具](install-client-tools.md)中的说明进行操作

### <a name="update-the-path-environment-variable"></a>更新 PATH 环境变量

请确保可从该客户端计算机上的任何位置调用这些工具。 例如，在 Windows 客户端计算机上，更新 PATH 系统环境变量并添加安装了 kubectl 的文件夹。

### <a name="sign-in-with-azdata"></a>登录方式 `azdata`

在开始 Azure Data Studio 之前，从此客户端计算机登录到 Arc 数据控制器。 为此，请运行命令，如下所示：

```console
azdata login --endpoint https://<IP address>:<port>
```

将替换 `<IP address>` 为 Kubernetes 群集的 IP 地址，将替换为 `<port>` Kubernetes 正在侦听的端口。 系统将提示你输入用户名和密码。 若要查看更多详细信息，请运行： _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>通过 kubectl 登录到 Kubernetes 群集

为此，你可能希望使用 [此](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) 博客文章中提供的示例命令。
您可以运行以下命令：

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>疑难解答笔记本

启动 Azure Data Studio 并打开故障排除笔记本。 

实现  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 中所述的步骤：

1. 连接到 Arc 数据控制器
2. 右键选择 Postgres 实例，然后选择 "**管理**"
3. 选择 " **诊断和解决问题" 仪表板**
4. 选择 " **疑难解答" 链接**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-打开 PostgreSQL 笔记本疑难解答":::

**TSG100-启用了 Azure Arc PostgreSQL 超大规模疑难解答笔记本**： :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-打开 PostgreSQL 笔记本疑难解答":::

#### <a name="run-the-scripts"></a>运行脚本
选择顶部的 "全部运行" 按钮，一次性执行笔记本，或者逐个单步执行并执行每个代码单元。

查看代码单元的执行输出中是否存在任何潜在问题。

随着时间的推移，我们将更详细地介绍如何识别常见问题以及如何解决这些问题。

## <a name="next-step"></a>后续步骤
- 了解如何 [为启用了 Azure Arc 的数据服务获取日志](troubleshooting-get-logs.md)
- 阅读有关[在 Kibana 中搜索日志的](monitor-grafana-kibana.md)信息
- 了解如何 [通过 Grafana 进行监视](monitor-grafana-kibana.md)
- 创建自己的笔记本
