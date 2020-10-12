---
title: 使用 Kibana 和 Grafana 查看日志和指标
description: 使用 Kibana 和 Grafana 查看日志和指标
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d876862d8f41ab8df646bef051629fd45c4d4601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934520"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>使用 Kibana 和 Grafana 查看日志和指标

提供了 Kibana 和 Grafana Web 仪表板，可让你深入了解启用了 Azure Arc 的数据服务所使用的 Kubernetes 命名空间。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>检索群集的 IP 地址

若要访问仪表板，你将需要检索群集的 IP 地址。 检索正确的 IP 地址的方法会有所不同，具体取决于你选择部署 Kubernetes 的方式。 单步执行下面的选项，找到合适的选项。

### <a name="azure-virtual-machine"></a>Azure 虚拟机

若要检索公共 IP 地址，请使用以下命令：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm 群集

若要检索群集 ip 地址，请使用以下命令：

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS 或其他负载平衡群集

若要监视 AKS 或其他负载平衡群集中的环境，需要获取管理代理服务的 ip 地址。 使用此命令检索 **外部 ip** 地址：

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>其他防火墙配置

你可能会发现需要在防火墙上打开端口才能访问 Kibana 和 Grafana 终结点。

下面是有关如何为 Azure VM 执行此操作的示例。 如果已使用脚本部署了 Kubernetes，则需要执行此操作。

下面的步骤重点介绍如何为 Kibana 和 Grafana 终结点创建 NSG 规则：

### <a name="find-the-name-of-the-nsg"></a>查找 NSG 的名称

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>添加 NSG 规则

获得 NSG 的名称后，可以使用以下命令添加规则：

```console
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>监视 Azure 上的 Azure SQL 托管实例

现在，你已经有了 IP 地址并公开了端口，接下来你应该能够访问 Grafana 和 Kibana。

> [!NOTE]
>  当系统提示输入用户名和密码时，请输入在创建 Azure Arc 数据控制器时提供的用户名和密码。

> [!NOTE]
>  由于预览版中使用的证书是自签名证书，因此会出现证书警告。

使用以下 URL 模式访问 Azure SQL 托管实例的日志记录和监视仪表板：

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

相关的仪表板包括：

* "Azure SQL 托管实例指标"
* "主机节点指标"
* "主机箱指标"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>监视 Azure Database for PostgreSQL 超大规模-Azure Arc

使用以下 URL 模式来访问 PostgreSQL 超大规模的日志记录和监视仪表板：

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

相关的仪表板包括：

* "Postgres 指标"
* "Postgres 表度量值"
* "主机节点指标"
* "主机箱指标"

## <a name="next-steps"></a>后续步骤
- 尝试 [将指标和日志上传到 Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- 阅读有关 Grafana 的信息：
   - [入门](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana 基础知识](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana 教程](https://grafana.com/tutorials/grafana-fundamentals/#1)
- 阅读有关 Kibana 的信息
   - [介绍](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana 指南](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [明细中的仪表板和 Kibana 中的数据可视化效果简介](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [如何生成 Kibana 仪表板](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

