---
title: 使用 Ansible（预览版）创建和配置 Azure Database for MySQL 服务器
description: 了解如何使用 Ansible 创建和配置 Azure Database for MySQL 服务器
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, 数据库
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: b549aeaf24bd774245ee1f2ff6924ac1f6dbeee3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427890"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible-preview"></a>使用 Ansible（预览版）创建和配置 Azure Database for MySQL 服务器
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) 是一种托管服务，可用于在云中运行、管理和缩放高可用性的 MySQL 数据库。 使用 Ansible 可以在环境中自动部署和配置资源。 

本快速入门介绍如何使用 Ansible 创建 Azure Database for MySQL 服务器并配置其防火墙规则。 可以使用 Azure 门户在大约五分钟内完成这些任务。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。 可以通过运行 `sudo pip install ansible[azure]==2.7.0rc2` 来安装 Ansible 2.7 RC 版本。 在 Ansible 2.7 发布之后，不需在此指定版本，因为默认版本会是 2.7。

## <a name="create-a-resource-group"></a>创建资源组
资源组是在其中部署和管理 Azure 资源的逻辑容器。  

以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

将上面的 playbook 另存为 **rg.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>创建 MySQL 服务器和数据库
以下示例创建名为 **mysqlserveransible** 的 MySQL 服务器，以及名为 **mysqldbansible** 的 Azure Database for MySQL 实例。 这是第 5 代基本用途服务器，带有一个 vCore。 

**mysqlserver_name** 的值必须唯一。 若要了解适用于每个区域和每个层的有效值，请参阅[定价层文档](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 将 `<server_admin_password>` 替换为密码。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

将上面的 playbook 另存为 **mysql_create.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>配置防火墙规则
服务器级防火墙规则允许外部应用程序通过 Azure MySQL 服务防火墙连接到服务器。 外部应用程序的示例是 **mysql** 命令行工具或 MySQL Workbench。
以下示例创建名为 **extenalaccess** 的防火墙规则，该规则允许从外部 IP 地址进行连接。 

为 **startIpAddress** 和 **endIpAddress** 输入自己的值。 使用 IP 地址范围，这些地址对应于要从其进行连接的位置。 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 这种情况下无法连接到服务器，除非 IT 部门打开了端口 3306。
> 

此处的 **azure_rm_resource** 模块用于执行此任务。 它允许直接使用 REST API。

将上面的 playbook 另存为 **mysql_firewall.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>使用命令行工具连接到服务器
可[下载 MySQL](https://dev.mysql.com/downloads/) 并将其安装在计算机上。 可以改为选择代码示例中的“试用”按钮或 Azure 门户中右上角工具栏的 **>_** 按钮，打开“Azure Cloud Shell”。

输入后续命令： 

1. 使用 **mysql** 命令行工具连接到服务器：
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. 查看服务器状态：
```sql
 mysql> status
```

如果一切顺利，命令行工具应输出以下文本：

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>使用事实来查询 MySQL 服务器
以下示例查询 **myResourceGroup** 中的 MySQL 服务器，然后查询该服务器上的所有数据库：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

将上面的 playbook 另存为 **mysql_query.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook mysql_query.yml
```

然后会看到 MySQL 服务器的以下输出： 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

此外，还会看到 MySQL 数据库的以下输出：
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>清理资源

如果不需要这些资源，可运行以下示例命令将其删除。 此命令删除名为 **myResourceGroup** 的资源组。 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

将上面的 playbook 另存为 **rg_delete.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook rg_delete.yml
```

如果只希望删除新创建的 MySQL 服务器，请运行以下示例：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

将上面的 playbook 另存为 **mysql_delete.yml**。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)
