---
title: 使用 Azure Data Studio 创建 Azure SQL 托管实例
description: 使用 Azure Data Studio 创建 Azure SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2aed3ea48935a62d68f739594a265649a2e1292d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311523"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>创建 SQL 托管实例-使用 Azure Data Studio 的 Azure Arc

本文档逐步讲解如何使用 Azure Data Studio 安装 Azure SQL 托管实例-Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>登录到 Azure Arc 数据控制器

如果尚未登录，请先登录到 Azure Arc 数据控制器，然后才能创建实例。

```console
azdata login
```

然后，系统将提示你输入创建数据控制器的命名空间、用于登录控制器的用户名和密码。  

> 如果需要验证命名空间，则可以运行 ```kubectl get pods -A``` 来获取群集上所有命名空间的列表。

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上创建 Azure SQL 托管实例

- 启动 Azure Data Studio
- 在 "连接" 选项卡上，单击左上角的三个点，然后选择 "新建部署"
- 从 "部署选项" 中，选择 " **AZURE SQL 托管实例-Azure Arc** " 
  > [!NOTE]
  > 如果当前未安装 azdata CLI，系统可能会提示您安装它。
- 接受隐私和许可条款，并单击底部的 " **选择** "



- 在 "部署 Azure SQL 托管实例-Azure Arc" 边栏选项卡中输入以下信息：
  - 输入 SQL Server 实例的名称
  - 输入并确认 SQL Server 实例的密码
  - 选择适用于数据的存储类
  - 选择适用于日志的存储类

- 单击 " **部署** " 按钮

- 这应该会在数据控制器上启动 Azure SQL 托管实例的 Azure Arc 创建。

- 几分钟后，创建应成功完成

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>连接到 Azure SQL 托管实例-Azure Arc from Azure Data Studio

- 通过提供数据控制器的命名空间、用户名和密码登录到 Azure Arc 数据控制器： 
```console
azdata login
```

- 使用以下命令查看预配的所有 Azure SQL 托管实例：

```console
azdata arc sql mi list
```

输出应如下所示，从此处复制 ServerEndpoint (包括端口号) 。

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- 在 Azure Data Studio 的 "**连接**" 选项卡下，单击 "**服务器**" 视图上的 "**新建" 连接**
- 在 " **连接** " 边栏选项卡中，将 ServerEndpoint 粘贴到 "服务器" 文本框中
- 选择 " **SQL 登录名** " 作为 "身份验证类型"
- 输入 *sa* 作为用户名
- 输入帐户的密码 `sa`
- （可选）输入要连接到的特定数据库名称
- 根据需要，选择/添加新的服务器组
- 选择 " **连接** " 以连接到 azure SQL 托管实例-azure Arc




## <a name="next-steps"></a>后续步骤

现在尝试 [监视 SQL 实例](monitor-grafana-kibana.md)
