---
title: 通过重定向连接到 Azure Database for MySQL
description: 本文介绍如何将应用程序配置为通过重定向连接到 Azure Database for MySQL。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233735"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>通过重定向连接到 Azure Database for MySQL

本主题说明如何使用重定向模式将应用程序连接到 Azure Database for MySQL 服务器。 重定向旨在通过允许应用程序直接连接到后端服务器节点，来减少客户端应用程序和 MySQL 服务器之间的网络延迟。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)中对重定向的支持目前以预览版提供。

## <a name="before-you-begin"></a>开始之前
登录到 [Azure 门户](https://portal.azure.com)。 创建具有引擎版本5.6、5.7 或8.0 的 Azure Database for MySQL 服务器。 有关详细信息，请参阅[如何在门户中创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。

当前仅在启用 SSL 时才支持重定向。 有关如何配置 SSL 的详细信息，请参阅[通过 Azure Database for MySQL 使用 SSL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure)。 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- PHP 梨 
- php-mysql
- 启用 SSL 的 Azure Database for MySQL 服务器

1. 通过[PECL](https://pecl.php.net/package/mysqlnd_azure)安装[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 通过运行以下内容查找扩展目录（`extension_dir`）：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 将目录更改为返回的文件夹，并确保 `mysqlnd_azure.so` 位于此文件夹中。 

4. 通过运行以下内容查找 .ini 文件的文件夹： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 将目录更改为此返回的文件夹。 

6. 为 `mysqlnd_azure`创建新的 .ini 文件。 请确保名称的字母表顺序晚于 mysqnld，因为将根据 ini 文件的名称顺序加载这些模块。 例如，如果 `mysqlnd` 名为 `10-mysqlnd.ini`，请将 mysqlnd ini 命名为 `20-mysqlnd-azure.ini`。

7. 在新的 .ini 文件中，添加以下行以启用重定向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>{1}Windows{2}

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- php-mysql
- 启用 SSL 的 Azure Database for MySQL 服务器

1. 通过运行以下命令确定是否正在运行 x64 或 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 从[PECL](https://pecl.php.net/package/mysqlnd_azure)中下载与 PHP 版本相匹配的相应 x64 或 x86 版本的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL。 

3. 提取 zip 文件，并找到名为 `php_mysqlnd_azure.dll`的 DLL。

4. 通过运行以下命令找到扩展目录（`extension_dir`）：

    ```cmd
    php -i | find "extension_dir"s
    ```

5. 将 `php_mysqlnd_azure.dll` 文件复制到步骤4中返回的目录中。 

6. 使用以下命令查找包含 `php.ini` 文件的 PHP 文件夹：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改 `php.ini` 文件，并添加以下额外行来启用重定向。 

    在 "动态扩展" 部分下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在 "模块设置" 部分下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>确认重定向

你还可以确认是否已将重定向配置为具有以下示例 PHP 代码。 创建名为 `mysqlConnect.php` 的 PHP 文件，并粘贴以下代码。 将服务器名称、用户名和密码更新为你自己的名称。 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>后续步骤
有关连接字符串的详细信息，请参阅[连接字符串](howto-connection-string.md)。

