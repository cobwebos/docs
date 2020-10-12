---
title: 获取用于排查启用了 Azure Arc 的数据控制器的日志
description: 获取用于排查启用了 Azure Arc 的数据控制器的服务日志。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934321"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>获取启用了 Azure Arc 的数据服务日志

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必备条件

若要检索启用了 Azure Arc 的数据服务日志，你将需要 Azure 数据 CLI 工具。 [安装说明](./install-client-tools.md)

你将需要以管理员身份登录到已启用 Azure Arc 的数据服务控制器服务。

## <a name="get-azure-arc-enabled-data-services-logs"></a>获取启用了 Azure Arc 的数据服务日志

可以在所有 pod 或特定的 pod 中获得启用了 Azure Arc 的数据服务日志，以便进行故障排除。  你可以使用标准的 Kubernetes 工具（如命令） `kubectl logs` 或在本文中使用 Azure 数据 CLI 工具，这样一来，就可以更轻松地一次获取所有日志。

首先，请确保已登录到数据控制器。

```console
azdata login
```

然后运行以下命令转储日志：
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

默认情况下，将在当前工作目录中创建日志文件，其中名为 "日志"。  可以使用参数将日志文件输出到不同的目录 `--target-folder` 。

您可以选择通过省略参数来压缩文件 `--skip-compress` 。

可以通过省略来触发和包含内存转储 `--exclude-dumps` ，但不建议这样做，除非 Microsoft 支持部门请求内存转储。  采用内存转储要求将数据控制器设置 `allowDumps` 设置为 `true` 创建数据控制器的时间。

您可以根据需要选择进行筛选，以仅收集特定 pod (`--pod`) 或容器 (`--container`) 按名称收集日志。

你还可以选择通过传递和参数来筛选以收集特定自定义资源的 `--resource-kind` 日志 `--resource-name` 。  `resource-kind`参数值应为可以通过命令检索的自定义资源定义名称之一 `kubectl get customresourcedefinition` 。

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

文件夹层次结构的示例。  请注意，文件夹层次结构按 pod 名称命名，然后按容器，然后按目录层次结构在容器中进行组织。

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```