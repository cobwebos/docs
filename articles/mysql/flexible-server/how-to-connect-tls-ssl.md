---
title: 在 Azure Database for MySQL 灵活的服务器中使用 TLS/SSL 进行加密连接
description: 有关如何使用 TLS/SSL 在 Azure Database for MySQL 灵活的服务器中进行连接的说明和信息。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934773"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>使用传输层安全性加密连接 (TLS 1.2) Azure Database for MySQL-灵活服务器

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

Azure Database for MySQL 灵活的服务器支持使用传输层安全性 (TLS) ，将客户端应用程序连接到 MySQL 服务，以前称为安全套接字层 (SSL) 。 TLS 是一种行业标准协议，可确保数据库服务器和客户端应用程序之间的加密网络连接，使你能够遵守符合性要求。

Azure Database for MySQL 灵活服务器仅支持使用传输层安全性 (TLS 1.2) 的加密连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有传入连接。 对于所有灵活的服务器，会启用 TLS 连接强制，并且不能禁用 TLS/SSL 来连接到灵活的服务器。

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>需要对 TLS/SSL 连接进行证书验证的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能实现安全连接。 Azure Database for MySQL 灵活的服务器使用 *DigiCert 全局根 CA*。 下载此证书以通过 SSL 与 [DigiCert 全局根 CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 进行通信，并将证书文件保存到首选位置。 例如，本教程使用 `c:\ssl`。

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>使用 mysql 命令行客户端与 TLS/SSL 连接

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

你可以选择 " [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) " 或 " [MySQL 工作台](./connect-workbench.md)"，> 从本地环境连接到服务器。 

下面的示例演示如何使用 mysql 命令行界面连接到你的服务器。 使用 `--ssl-mode=REQUIRED` 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 `--ssl-ca` 参数。 将值替换为实际的服务器名称和密码。 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> 确认传递给 `--ssl-ca` 的值与你保存的证书的文件路径匹配。

### <a name="verify-the-tlsssl-connection"></a>验证 TLS/SSL 连接

执行 mysql **状态** 命令，以验证是否已使用 TLS/SSL 连接到 mysql 服务器：

```dos
mysql> status
```
通过查看输出来确认连接是否已加密，该输出应显示：  **SSL：密码正在使用中是 AES256-SHA**。 此密码套件显示一个示例，基于客户端，可以看到不同的密码套件。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保应用程序或框架支持 TLS 连接

默认情况下，在安装过程中，使用 MySQL 作为数据库服务的一些应用程序框架不会启用 TLS。 MySQL server 强制执行 TLS 连接，但如果没有为 TLS 配置应用程序，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 TLS 连接。

## <a name="sample-code"></a>示例代码
在 Azure 门户中为服务器提供的 "连接字符串" 页中预定义的连接字符串包括使用 TLS/SSL 连接到数据库服务器所需的公共语言参数。 TLS/SSL 参数根据连接器的不同而异。 例如，"useSSL = true"、"sslmode = required" 或 "ssl_verify_cert = true" 和其他变体。

若要从应用程序通过 TLS/SSL 与灵活的服务器建立加密连接，请参阅以下代码示例：

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP（使用 PDO）

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java（适用于 Java 的 MySQL 连接器）

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java（适用于 Java 的 MariaDB 连接器）

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>后续步骤
- [使用 MySQL 工作台在 Azure Database for MySQL 灵活的服务器中连接和查询数据](./connect-workbench.md)
- [使用 PHP 在 Azure Database for MySQL 灵活的服务器中连接和查询数据](./connect-php.md)
- [使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活的服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活的服务器防火墙规则](./concepts-networking.md#public-access-allowed-ip-addresses)
