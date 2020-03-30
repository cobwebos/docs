---
title: 使用重定向连接 - MySQL 的 Azure 数据库
description: 本文介绍如何配置应用程序以通过重定向连接到 MySQL 的 Azure 数据库。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246325"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>通过重定向连接到 MySQL 的 Azure 数据库

本主题介绍如何使用重定向模式连接 MySQL 服务器的应用程序。 重定向旨在通过允许应用程序直接连接到后端服务器节点来减少客户端应用程序和 MySQL 服务器之间的网络延迟。

## <a name="before-you-begin"></a>开始之前
登录到 Azure[门户](https://portal.azure.com)。 为具有引擎版本 5.6、5.7 或 8.0 的 MySQL 服务器创建 Azure 数据库。 有关详细信息，请参阅[如何在门户中创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。

当前仅在 MySQL 服务器的 Azure 数据库**上启用 SSL**时才支持重定向。 有关如何配置 SSL 的详细信息，请参阅[将 SSL 与 Azure 数据库一起使用 MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)。

## <a name="php"></a>PHP

通过 Microsoft 开发的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)扩展，可以支持 PHP 应用程序中的重定向。 

mysqlnd_azure扩展可以通过PECL添加到PHP应用程序，强烈建议通过正式发布的[PECL包](https://pecl.php.net/package/mysqlnd_azure)安装和配置扩展。

> [!IMPORTANT]
> 对 PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)扩展中重定向的支持当前处于预览状态。

### <a name="redirection-logic"></a>重定向逻辑

>[!IMPORTANT]
> 重定向逻辑/行为开始版本 1.1.0 已更新 **，建议使用版本 1.1.0+**。

重定向行为由 的值`mysqlnd_azure.enableRedirect`确定。 下表基于版本**1.1.0+** 中此参数的值概述了重定向的行为。

如果使用旧版本的mysqlnd_azure扩展版（版本 1.0.0-1.0.3），重定向行为由`mysqlnd_azure.enabled`的值确定。 有效值是`off`（作用类似于下表中概述的行为）和`on`（如下`preferred`表所示）。  

|**mysqlnd_azure.启用重定向值**| **行为**|
|----------------------------------------|-------------|
|`off` 或 `0`|不会使用重定向。 |
|`on` 或 `1`|- 如果 MySQL 服务器的 Azure 数据库未启用 SSL，则不会进行连接。 将返回以下错误 *："mysqlnd_azure.enableredirect 处于打开，但连接字符串中未设置 SSL 选项。只有使用 SSL 才能重定向。*<br>- 如果在 MySQL 服务器上启用了 SSL，但服务器上不支持重定向，则第一个连接将中止，并返回以下错误 *："连接已中止，因为 MySQL 服务器上未启用重定向或网络包不符合重定向协议。*<br>- 如果 MySQL 服务器支持重定向，但重定向的连接由于任何原因失败，则也会中止第一个代理连接。 返回重定向连接的错误。|
|`preferred` 或 `2`<br> （默认值）|- mysqlnd_azure将尽可能使用重定向。<br>- 如果连接不使用 SSL，服务器不支持重定向，或者重定向的连接由于任何非致命原因无法连接，而代理连接仍然是有效的连接，它将回落到第一个代理连接。|

本文档的后续部分将概述如何使用 PECL 安装`mysqlnd_azure`扩展并设置此参数的值。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15° 和 7.3.2°
- PHP PEAR 
- php-mysql
- 启用 SSL 的 MySQL 服务器的 Azure 数据库

1. 使用[PECL](https://pecl.php.net/package/mysqlnd_azure)安装[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 。 建议使用版本 1.1.0+。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 通过运行以下操作找到`extension_dir`扩展目录 （ ）：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 将目录更改为返回的文件夹，并确保`mysqlnd_azure.so`位于此文件夹中。 

4. 通过运行以下功能找到 .ini 文件的文件夹： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 将目录更改为此返回的文件夹。 

6. 为`mysqlnd_azure`创建新 .ini 文件。 确保名称的字母顺序位于 mysqnld 之后，因为模块是根据 ini 文件的名称顺序加载的。 例如，如果`mysqlnd`名为`10-mysqlnd.ini`.ini，则将 mysqlnd ini`20-mysqlnd-azure.ini`命名为 。

7. 在新的 .ini 文件中，添加以下行以启用重定向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15° 和 7.3.2°
- php-mysql
- 启用 SSL 的 MySQL 服务器的 Azure 数据库

1. 通过运行以下命令确定是否运行 x64 或 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 从[PECL](https://pecl.php.net/package/mysqlnd_azure)下载与您的 PHP 版本的匹配的[相应 x64](https://github.com/microsoft/mysqlnd_azure)或 x86 版本的mysqlnd_azure DLL。 建议使用版本 1.1.0+。

3. 提取 zip 文件并找到名为`php_mysqlnd_azure.dll`的 DLL。

4. 通过运行以下命令找到`extension_dir`扩展目录 （ ）：

    ```cmd
    php -i | find "extension_dir"
    ```

5. 将`php_mysqlnd_azure.dll`文件复制到步骤 4 中返回的目录中。 

6. 使用以下命令查找包含`php.ini`文件的 PHP 文件夹：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改`php.ini`文件并添加以下额外行以启用重定向。 

    在"动态扩展"部分下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在"模块设置"部分下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>确认重定向

您还可以确认重定向配置了下面的 PHP 代码示例。 创建一个称为`mysqlConnect.php`PHP 文件的 PHP 文件，并粘贴以下代码。 使用您自己的服务器名称、用户名和密码更新服务器名称、用户名和密码。 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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
