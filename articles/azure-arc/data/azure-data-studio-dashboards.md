---
title: Azure Data Studio 仪表板
description: Azure Data Studio 仪表板
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107566"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio 仪表板

[Azure Data Studio](/sql/azure-data-studio/what-is) 提供了类似于查看有关 Azure Arc 资源的信息的 Azure 门户的体验。  这些视图称为 " **仪表板** "，其布局和选项与 Azure 门户中的给定资源类似，但在你的环境中，如果你没有 Azure 可用的连接，则可以灵活地在环境中查看该信息。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>连接到数据控制器

### <a name="prerequisites"></a>必备条件

- 下载 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- 已安装 Azure Arc 扩展

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>确定数据控制器服务器 API 终结点 URL

首先，需要将 Azure Data Studio 连接到数据控制器服务 API 终结点 URL。

若要获取此终结点，你可以运行以下命令：

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

你将看到如下所示的输出：

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

如果使用的是 LoadBalancer 类型，则需要复制外部 IP 地址和端口号。 如果使用的是 NodePort，则需要使用 Kubernetes API 服务器的 IP 地址和端口号，该端口号在端口 (S) 列中列出。

现在，你将需要通过组合如下信息来构造终结点的 URL：

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

记下你的 IP 地址，因为你将在下一步中使用它。

### <a name="connect"></a>连接

1. 打开 Azure Data Studio

1. 选择左侧的 " **连接** " 选项卡

在底部，展开称为 **Azure Arc 控制器**的面板。

单击 "+" 图标以添加新的数据控制器连接。

在 "命令" 面板的屏幕顶部，输入在步骤1中构造的 URL，单击 "输入"。
输入数据控制器的用户名。  这是在数据控制器部署过程中传递的用户名值。  单击 "enter"。
输入数据控制器的密码。  这是在数据控制器部署过程中传递的密码值。 单击 "enter"。

现在，你已连接到数据控制器，可以查看数据控制器的仪表板以及你拥有的任何 SQL 托管实例或 PostgreSQL 超大规模服务器组资源。

## <a name="view-the-data-controller-dashboard"></a>查看数据控制器仪表板

右键单击 " **Arc 控制器** " 面板中的 "连接" 面板中的数据控制器，然后选择 " **管理**"。

可在此处查看有关数据控制器资源的详细信息，例如名称、区域、连接模式、资源组、订阅、控制器终结点和命名空间。  您还可以查看数据控制器管理的所有托管数据库资源的列表。

你会注意到布局类似于你可能在 Azure 门户中看到的内容。

您可以方便地通过单击 "+ 新建实例" 按钮来启动 SQL 托管实例或 PostgreSQL 超大规模服务器组的创建。

还可以通过单击 "在 Azure 门户中打开" 按钮，在上下文中打开 Azure 门户。

## <a name="view-the-sql-managed-instance-dashboards"></a>查看 SQL 托管实例仪表板

如果已创建了一些 SQL 托管实例，则可以在管理它们的数据控制器下的 "Azure 数据控制器可展开" 面板中的 "连接" 面板中看到它们。

若要查看给定实例的 SQL 托管实例仪表板，请右键单击该实例，然后选择 "管理"。

连接面板将弹出右侧，并提示您输入登录名/密码以连接到该 SQL 实例。 如果你知道可以输入的连接信息，请单击 "连接"。  如果不知道，可以单击 "取消"。  无论采用哪种方式，都将在连接面板关闭时转到仪表板。

在 "概述" 选项卡上，可以查看有关 SQL 托管实例的详细信息，例如资源组、数据控制器、订阅 ID、状态、区域等。  你还可以看到链接，你可以单击该链接以进入该 SQL 托管实例的上下文中的 Grafana 或 Kibana 仪表板。

如果能够连接到 SQL 管理实例，可以在此处查看其他信息。

你可以从此处删除 SQL 托管实例，或者打开 Azure 门户以查看 Azure 门户中的 SQL 托管实例。

如果单击左侧的 "连接字符串" 选项卡，则可以看到该 SQL 托管实例的预先构造连接字符串列表，使你能够轻松地将其复制/粘贴到各种其他应用程序或代码中。

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>查看 PostgreSQL 超大规模服务器组仪表板

如果已创建了一些 PostgreSQL 超大规模服务器组，则可以在管理它们的数据控制器下的 "Azure 数据控制器" 中的 "连接" 面板中看到它们列出。

若要查看给定服务器组的 PostgreSQL 超大规模服务器组仪表板，请右键单击该服务器组，然后选择 "管理"。

在 "概述" 选项卡上，可以查看有关服务器组的详细信息，例如资源组、数据控制器、订阅 ID、状态、区域等。  你还可以看到链接，你可以单击该链接以进入该服务器组上下文中的 Grafana 或 Kibana 仪表板。

你可以从此处删除服务器组，或打开 Azure 门户以查看 Azure 门户中的服务器组。

如果单击左侧的 "连接字符串" 选项卡，则可以看到该服务器组的预构造连接字符串列表，使你可以轻松地将其复制/粘贴到各种其他应用程序或代码中。

如果单击左侧的 "属性" 选项卡，可以看到更多详细信息。

如果单击左侧的 "资源运行状况" 选项卡，可以看到该服务器组的当前高级运行状况。

如果单击左侧的 "诊断和解决问题" 选项卡，则可以启动 PostgreSQL 故障排除笔记本。

如果单击左侧的 "新建支持请求" 选项卡，则可以在服务器组的上下文中启动 Azure 门户，并从该处创建 Azure 支持请求。