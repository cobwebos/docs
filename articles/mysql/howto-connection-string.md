---
title: "如何将应用程序连接到 Azure Database for MySQL | Microsoft Docs"
description: "本文档中列出了目前支持的用于将应用程序连接到 Azure Database for MySQL 的所有连接字符串"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>如何将应用程序连接到 Azure Database for MySQL
本文档列出了 Azure Database for MySQL 支持的所有连接字符串，以及模板和示例。 连接字符串中的参数和设置可能各不相同。

- 请参阅文档[如何配置 SSL](./howto-configure-ssl.md)以获取证书
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

例如，假设服务器名称为 wpdemo，数据库名称为 wpdb，用户名为 WPAdmin，密码为 orcas!2。

则连接字符串应为：

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>从门户中获取连接字符串
在 [Azure 门户](https://portal.azure.com)中，转到 Azure Database for MySQL，然后单击“连接字符串”即可获取实例的字符串列表：![门户中的连接字符串](./media/howto-connection-strings/connection-strings-on-portal.png)

字符串提供驱动程序、服务器和其他数据库连接参数等详细信息。 根据需要复制连接字符串，然后使用数据库名称、密码等自己的参数进行修改。然后便可使用此字符串连接服务器。

## <a name="next-steps"></a>后续步骤
- 有关连接库的详细信息，请参阅[概念 - 连接库](./concepts-connection-libraries.md)

