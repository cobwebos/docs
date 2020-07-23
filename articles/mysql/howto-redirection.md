---
title: 通过重定向连接 - Azure Database for MySQL
description: 本文介绍了如何将应用程序配置为通过重定向连接到 Azure Database for MySQL。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: be660101a28d5ef289de1b25f8f7d33fbe9f617b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107812"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>通过重定向连接到 Azure Database for MySQL

本主题说明如何使用重定向模式将应用程序连接到 Azure Database for MySQL 服务器。 重定向旨在通过允许应用程序直接连接到后端服务器节点，来减少客户端应用程序和 MySQL 服务器之间的网络延迟。

## <a name="before-you-begin"></a>开始之前
登录 [Azure 门户](https://portal.azure.com)。 使用引擎版本 5.6、5.7 或 8.0 创建 Azure Database for MySQL 服务器。 

有关详细信息，请参阅如何使用[Azure 门户](quickstart-create-mysql-server-database-using-azure-portal.md)或[Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)创建 Azure Database for MySQL 服务器。

## <a name="enable-redirection"></a>启用重定向

在 Azure Database for MySQL 服务器上，将 `redirect_enabled` 参数配置为 `ON` 以允许处于重定向模式的连接。 若要更新此服务器参数，请使用[Azure 门户](howto-server-parameters.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)。

## <a name="php"></a>PHP

可通过 Microsoft 开发的 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展提供对 PHP 应用程序中重定向的支持。 

通过 PECL 可以将 mysqlnd_azure 扩展添加到 PHP 应用程序，强烈建议通过正式发布的 [PECL 包](https://pecl.php.net/package/mysqlnd_azure)安装和配置该扩展。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展中的重定向支持目前为预览版。

### <a name="redirection-logic"></a>重定向逻辑

>[!IMPORTANT]
> 自版本 1.1.0 起，重定向逻辑/行为已更新，**建议使用版本 1.1.0+** 。

重定向行为取决于 `mysqlnd_azure.enableRedirect` 的值。 下表概述了自版本 1.1.0+ 起基于此参数值进行重定向的行为。

如果使用的是较旧版本的 mysqlnd_azure 扩展（版本 1.0.0-1.0.3），则重定向行为取决于 `mysqlnd_azure.enabled` 的值。 有效值为 `off`（类似于下表中所述的行为）和 `on`（类似于下表中 `preferred` 的行为）。  

|**mysqlnd_azure.enableRedirect 值**| **行为**|
|----------------------------------------|-------------|
|`off` 或 `0`|将不使用重定向。 |
|`on` 或 `1`|- 如果连接未在驱动程序端使用 SSL，则不会建立连接。 将返回以下错误：“mysqlnd_azure.enableRedirect 为 on，但连接字符串中未设置 SSL 选项。仅在使用了 SSL 的情况下才支持重定向。”<br>- 如果在驱动程序端使用了 SSL，但服务器不支持重定向，则第一个连接将中止并返回以下错误：“由于 MySQL 服务器上未启用重定向，或网络包不符合重定向协议，连接已中止。”<br>- 如果 MySQL 服务器支持重定向，但重定向的连接因任何原因而失败，还会中止第一个代理连接。 返回重定向连接的错误。|
|`preferred` 或 `2`<br> （默认值）|- mysqlnd_azure 将尽可能使用重定向。<br>- 如果连接未在驱动程序端使用 SSL，则服务器不支持重定向，或者重定向的连接由于任何非致命原因而无法连接，而代理连接仍然有效，则服务器将回退到第一个代理连接。|

本文档的后续部分将概述如何使用 PECL 安装 `mysqlnd_azure` 扩展并设置此参数的值。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15+ 和 7.3.2+
- PHP PEAR 
- php-mysql
- Azure Database for MySQL 服务器

1. 通过 [PECL](https://pecl.php.net/package/mysqlnd_azure) 安装 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)。 建议使用版本 1.1.0+。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 通过运行以下命令查找扩展目录 (`extension_dir`)：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 将目录更改为返回的文件夹，并确保 `mysqlnd_azure.so` 位于此文件夹中。 

4. 通过运行以下命令查找 .ini 文件所在的文件夹： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 将目录更改为此返回的文件夹。 

6. 为 `mysqlnd_azure` 创建新的 .ini 文件。 请确保该文件名的字母顺序排在 mysqnld 后面，因为将根据 ini 文件的名称顺序加载这些模块。 例如，如果 `mysqlnd` .ini 名为 `10-mysqlnd.ini`，请将 mysqlnd ini 命名为 `20-mysqlnd-azure.ini`。

7. 在新的 .ini 文件中，添加以下行以启用重定向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>先决条件 
- PHP 版本 7.2.15+ 和 7.3.2+
- php-mysql
- Azure Database for MySQL 服务器

1. 通过运行以下命令确定运行的是 x64 还是 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 从 [PECL](https://pecl.php.net/package/mysqlnd_azure) 中下载与 PHP 版本相匹配的相应 x64 或 x86 版本的 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)。 建议使用版本 1.1.0+。

3. 提取 zip 文件，并找到名为 `php_mysqlnd_azure.dll` 的 DLL。

4. 通过运行以下命令查找扩展目录 (`extension_dir`)：

    ```cmd
    php -i | find "extension_dir"
    ```

5. 将 `php_mysqlnd_azure.dll` 文件复制到步骤 4 中返回的目录下。 

6. 使用以下命令查找包含 `php.ini` 文件的 PHP 文件夹：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改 `php.ini` 文件，并添加以下附加的行来启用重定向。 

    在“动态扩展”部分下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在“模块设置”部分下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>确认重定向

还可以确认是否已将重定向配置为具有以下示例 PHP 代码。 创建名为 `mysqlConnect.php` 的文件，并粘贴以下代码。 将服务器名称、用户名和密码更新为自己的。 
 
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
