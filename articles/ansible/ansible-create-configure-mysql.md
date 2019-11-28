---
title: 教程 - 使用 Ansible 在 Azure Database for MySQL 中配置数据库
description: 了解如何使用 Ansible 创建和配置 Azure Database for MySQL 服务器
keywords: ansible, azure, devops, bash, playbook, mysql, 数据库
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d8c8fd361f37a9cb961012807fe40b905c10c047
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156530"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>教程：使用 Ansible 在 Azure Database for MySQL 中配置数据库

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) 是一种基于 MySQL Community Edition 的关系数据库服务。 通过 Azure Database for MySQL，可在 Web 应用中管理 MySQL 数据库。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建 MySQL 服务器
> * 创建 MySQL 数据库
> * 配置防火墙规则，以便外部应用可连接到服务器
> * 从 Azure Cloud Shell 连接到 MySQL 服务器
> * 查询可用的 MySQL 服务器
> * 列出已连接服务器中的所有数据库

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>创建资源组

本部分中的 playbook 代码将创建一个 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。  

将以下 playbook 保存为 `rg.yml`：

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

运行 playbook 之前，请参阅以下说明：

* 创建名为 `myResourceGroup` 的资源组。
* 在 `eastus` 位置创建该资源组：

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>创建 MySQL 服务器和数据库

本部分中的 playbook 代码创建 MySQL 服务器和 Azure Database for MySQL 实例。 新的 MySQL 服务器是 Gen 5 Basic Purpose 服务器，带有一个 vCore，名为 `mysqlserveransible`。 数据库实例被命名为 `mysqldbansible`。

有关详细信息，请参阅 [Azure Database for MySQL 定价层](/azure/mysql/concepts-pricing-tiers)。 

将以下 playbook 保存为 `mysql_create.yml`：

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

运行 playbook 之前，请参阅以下说明：

* 在 `vars` 部分中，`mysqlserver_name` 的值必须是唯一的。
* 在 `vars` 部分中，使用密码替换 `<server_admin_password>`。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>配置防火墙规则

服务器级防火墙规则允许外部应用通过 Azure MySQL 服务防火墙连接到服务器。 `mysql` 命令行工具和 MySQL Workbench 就是外表应用示例。

本本部中的 playbook 代码创建一个名为 `extenalaccess` 的防火墙规则，该规则允许从任何外部 IP 地址进行连接。 

将以下 playbook 保存为 `mysql_firewall.yml`：

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

运行 playbook 之前，请参阅以下说明：

* 在变量部分中，替换 `startIpAddress` 和 `endIpAddress`。 使用与要连接的范围对应的 IP 地址范围。
* 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 这种情况下无法连接到服务器，除非 IT 部门打开了端口 3306。
* Playbook 使用 `azure_rm_resource` 模块，该模块允许直接使用 REST API。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>连接到服务器

本部分将使用 Azure Cloud Shell 连接到先前创建的服务器。

1. 在以下代码中选择“试用”按钮  ：

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. 在提示框中，输入以下命令以查询服务器状态：

    ```sql
    mysql> status
    ```
    
    如果一切顺利，可看到类似于以下结果的输出：
    
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
    
## <a name="query-mysql-servers"></a>查询 MySQL 服务器

本部分中的 playbook 代码查询 `myResourceGroup` 中的 MySQL 服务器并列出找到的服务器上的数据库。

将以下 playbook 保存为 `mysql_query.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook mysql_query.yml
```

运行 playbook 后，可看到类似于以下结果的输出：

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

此外，还可看到 MySQL 数据库的以下输出：

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

如果不再需要本教程中创建的资源，请将其删除。 

将以下 playbook 保存为 `cleanup.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible/)