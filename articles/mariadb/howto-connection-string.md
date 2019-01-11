---
title: 将应用程序连接到 Azure Database for MariaDB
description: 本文档列出了目前支持用于将应用程序连接到 Azure Database for MariaDB 的连接字符串，包括 ADO.NET (C#)、JDBC、Node.js、ODBC、PHP、Python 和 Ruby。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536568"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>如何将应用程序连接到 Azure Database for MariaDB
本主题列出了 Azure Database for MariaDB 支持的连接字符串类型，并收录了模板和示例。 你可能会在连接字符串中使用不同的参数和设置。

- 若要获取证书，请参阅[如何配置 SSL](./howto-configure-ssl.md)。
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = userID 格式是用于身份验证的正确格式。  如果只使用 userID，身份验证将失败。

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

在此示例中，服务器名称为 `mydemoserver`，数据库名称为 `wpdb`，用户名为 `WPAdmin`，密码为 `mypassword!2`。 因此，连接字符串应为：

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>从 Azure 门户获取连接字符串详细信息
在 [Azure 门户](https://portal.azure.com)中，转到 Azure Database for MariaDB 服务器，再单击“连接字符串”，以获取实例的字符串列表：![Azure 门户中的“连接字符串”窗格](./media/howto-connection-strings/connection-strings-on-portal.png)

字符串提供驱动程序、服务器和其他数据库连接参数等详细信息。 修改这些示例以使用自己的参数，例如数据库名称、密码等。 然后，可以在代码和应用程序中使用此字符串连接到服务器。

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
