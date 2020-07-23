---
title: 连接并查询 Azure SQL Edge（预览版）
description: 了解如何连接到 Azure SQL Edge 并进行查询（预览）。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6d82446a915863e6aa95cc79a421f86b8c4dd3a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85252638"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>连接并查询 Azure SQL Edge（预览版）

在 Azure SQL Edge 中，部署容器后，可以从以下任意位置连接到数据库引擎：

- 在容器内部
- 从同一主机上运行的另一个 Docker 容器
- 从主机
- 从网络上的任何其他客户端计算机

## <a name="tools-to-connect-to-azure-sql-edge"></a>连接到 Azure SQL Edge 的工具

可以通过以下任一常用工具连接到 Azure SQL Edge 实例的实例：

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)： sqlcmd 客户端工具已包含在 Azure SQL Edge 的容器映像中。 如果使用交互式 bash shell 附加到正在运行的容器，则可以在本地运行这些工具。
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

若要从网络计算机连接到 Azure SQL Edge 数据库引擎，需要以下各项：

- **主机的 IP 地址或网络名称**：这是运行 Azure SQL Edge 容器的主机。
- **AZURE SQL Edge 容器主机端口映射**：这是 Docker 容器端口到主机端口的映射。 在容器中，Azure SQL Edge 始终映射到端口1433。 如果需要，可以对此进行更改。 若要更改端口号，请在 Azure IoT Edge 中更新 Azure SQL Edge 模块的**容器创建选项**。 在以下示例中，容器上的端口1433映射到主机上的端口1600。

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **AZURE Sql edge 实例的 SA 密码**：这是 `SA_PASSWORD` 在部署 Azure sql edge 期间为环境变量指定的值。

## <a name="connect-to-the-database-engine-from-within-the-container"></a>从容器内连接到数据库引擎

[SQL Server 的命令行工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)包含在 Azure SQL Edge 的容器映像中。 如果使用交互式命令提示符附加到容器，则可以在本地运行这些工具。

1. 使用 `docker exec -it` 命令在运行的容器内部启动交互式 Bash Shell。 在下面的示例中， `e69e056c702d` 是容器 ID。

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > 并非始终需要指定整个容器 ID。 只需指定能够唯一标识它的足够字符即可。 在此示例中，可以使用 `e6` 或 `e69` ，而不是完整的 ID。

2. 在容器中时，请用 sqlcmd 本地连接。 默认情况下，Sqlcmd 不在路径中，因此需要指定完整路径。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. 完成 sqlcmd 后，键入 `exit` 。

4. 完成交互式命令提示符时，请键入 `exit` 。 退出交互式 Bash Shell 后，容器将继续运行。

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>从同一主机上的另一个容器连接到 Azure SQL Edge

由于同一主机上运行的两个容器位于同一 Docker 网络上，因此可以使用服务的容器名称和端口地址轻松访问这些容器。 例如，如果要从同一主机上的另一个 python 模块（容器）连接到 Azure SQL Edge 的实例，则可以使用类似于下面的连接字符串。 （此示例假定 Azure SQL Edge 配置为侦听默认端口。）

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>从另一台网络计算机连接到 Azure SQL Edge

你可能需要从网络上的另一台计算机连接到 Azure SQL Edge 的实例。 为此，请使用 Docker 主机的 IP 地址和 Azure SQL Edge 容器映射到的主机端口。 例如，如果 Docker 主机的 IP 地址是*xxx.xxx.xxx.xxx*，而 Azure sql edge 容器映射到主机端口*1600*，则 azure sql edge 实例的服务器地址将为 xxx。 xxx *，1600*。 更新的 python 脚本是：

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

若要使用 Windows 计算机上运行 SQL Server Management Studio 连接到 Azure SQL Edge 的实例，请参阅[SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms)。

若要使用 Windows、Mac 或 Linux 计算机上的 Visual Studio Code 连接到 Azure SQL Edge 的实例，请参阅[Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)。

若要使用 Windows、Mac 或 Linux 计算机上的 Azure Data Studio 连接到 Azure SQL Edge 的实例，请参阅[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server)。

## <a name="next-steps"></a>后续步骤

[连接和查询](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[在 Linux 上安装 SQL Server 工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
