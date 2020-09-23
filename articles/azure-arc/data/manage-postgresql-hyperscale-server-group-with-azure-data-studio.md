---
title: 使用 Azure Data Studio 管理 PostgreSQL 实例
description: 使用 Azure Data Studio 管理 PostgreSQL 实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fc0ad45f575f9190f15b61acdf476c716b7f1638
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934822"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>使用 Azure Data Studio 管理启用了 Azure Arc 的 PostgreSQL 超大规模服务器组


本文介绍如何：
- 管理 PostgreSQL 实例，其中包含仪表板视图，如概述、连接字符串、属性资源运行状况 .。。
- 使用你的数据和架构

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必备知识

- [安装 azdata、Azure Data Studio 和 Azure CLI](install-client-tools.md)
- 在 **Azure 数据 CLI** 和 **Azure Arc** 和 **PostgreSQL** 扩展中安装 Azure Data Studio
- 创建 [Azure Arc 数据控制器](create-data-controller-using-azdata.md)
- 启动 Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>连接到 Azure Arc 数据控制器

在 Azure Data Studio 中，展开 " **Azure Arc 控制器** " 节点，并选择 " **连接控制器** " 按钮：

输入 Azure 数据控制器的连接信息：

- **控制器 URL：**

    用于连接到 Kubernetes 中的控制器的 URL。 以形式输入 `https://<IP_address_of_the_controller>:<Kubernetes_port.` ，例如：

    ```console
    https://12.345.67.890:30080
    ```
- **用户名：**

    用于连接到控制器的用户帐户的名称。 使用在运行时通常使用的名称 `azdata login` 。 它不是用于从 psql 连接到 PostgreSQL 数据库引擎的 PostgreSQL 用户的名称。
- **密码：** 用于连接到控制器的用户帐户的密码


Azure data studio 显示 Arc 数据控制器。 展开它并显示它管理的 PostgreSQL 实例的列表。

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>管理启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

右键单击要管理的 PostgreSQL 实例，然后选择 "管理"

PostgreSQL 仪表板视图：

此窗格左侧列出了多个仪表板：

- **概述：** 显示有关实例的摘要信息，如名称、Azure 订阅 ID、配置、数据库引擎版本、Grafana 和 Kibana 的终结点 .。。
- **连接字符串：** 显示连接到 PostgreSQL 实例所需的各种连接字符串，例如 psql、Node.js、PHP、Ruby .。。
- **属性：** 显示不同的属性，例如 PostgreSQL 管理员用户名、阴影资源的关联资源组 .。。
- **诊断并解决问题：** 是登陆页面，您可以在其中找到各种资源，这些资源将帮助您在展开故障排除笔记本时对实例进行故障排除
- **新的支持请求：** 是一个登陆页面，你将能够从我们的支持服务中请求协助，并从公共预览版公告开始。

## <a name="work-with-your-data-and-schema"></a>使用你的数据和架构

在 "Azure Data Studio" 窗口左侧，展开节点 **服务器**：

然后选择 "添加连接"，并在 PostgreSQL 实例中填写连接详细信息：
- **连接类型：** PostgreSQL
- **服务器名称：** 输入 PostgreSQL 实例的名称。 例如： postgres01
- **身份验证类型：** 权限
- **用户名：** 例如，你可以使用标准/默认 PostgreSQL 管理员用户名。 请注意，此字段区分大小写。
- **密码：** 你会在命令输出中的 psql 连接字符串中找到 PostgreSQL 用户名的密码 `azdata postgres server endpoint -n postgres01`
- **数据库名称：** 设置要连接到的数据库的名称。 可以将其设置为 __默认值__
- **服务器组：** 可以将其设置为 __默认值__
- **名称 (可选) ：** 可以将其留空
- **高**
    - **主机 IP 地址：** 是 Kubernetes 群集的公共 IP 地址
    - **端口：** 是 PostgreSQL 实例侦听的端口。 可以在命令输出中的 psql 连接字符串的末尾找到此端口 `azdata postgres server endpoint -n postgres01` 。 不是 Kubernetes 正在侦听的端口30080，并且在连接到 Azure Data Studio 中的 Azure 数据控制器时输入。
    - **其他参数：** 它们应该是自显式的，你可以将它们与一起显示的默认值/空白值一起显示。

选择 **"确定" 和 "连接"** 以连接到服务器。

连接后，可以使用几个体验：
- **新查询**
- **新笔记本**
- **展开服务器的显示，并浏览/处理数据库中的对象**
- **...**

## <a name="next-step"></a>下一步
[监视你的服务器组](monitor-grafana-kibana.md)
