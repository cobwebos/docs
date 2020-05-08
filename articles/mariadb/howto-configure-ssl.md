---
title: 配置 SSL - Azure Database for MariaDB
description: 介绍了如何正确配置 Azure Database for MariaDB 和关联的应用程序，以正确使用 SSL 连接
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/7/2020
ms.openlocfilehash: 9c5f4b1602182b3f9dca45c11d572e1fec2ff07c
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925960"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>配置应用程序的 SSL 连接性以安全连接到 Azure Database for MariaDB
Azure Database for MariaDB 支持使用安全套接字层 (SSL) 将 Azure Database for MariaDB 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

## <a name="obtain-ssl-certificate"></a>获取 SSL 证书
从 [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 下载通过 SSL 与 Azure Database for MariaDB 服务器通信所需的证书，再将证书文件保存到本地驱动器（例如，本教程使用 c:\ssl）。
**对于 Microsoft Internet Explorer 和 Microsoft Edge：** 下载完成之后，将证书重命名为 BaltimoreCyberTrustRoot.crt.pem。

## <a name="bind-ssl"></a>绑定 SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 通过 SSL 连接到服务器
配置 MySQL Workbench，以便安全地通过 SSL 连接。 

1. 从“设置新连接”对话框，导航到“SSL”选项卡  。 

1. 将“使用 SSL”  字段更新为“必需”。

1. 在“SSL CA 文件:”字段中输入 BaltimoreCyberTrustRoot.crt.pem 的文件位置********。 
    
    ![保存 SSL 配置](./media/howto-configure-ssl/mysql-workbench-ssl.png)

对于现有连接，可以通过右键单击“连接”图标并选择“编辑”来绑定 SSL。 然后导航到“SSL”  选项卡，并绑定证书文件。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 通过 SSL 连接到服务器
绑定 SSL 证书的另一种方法是使用 MySQL 命令行接口执行以下命令。 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> 在 Windows 上使用 MySQL 命令行接口时，可能会收到错误 `SSL connection error: Certificate signature check failed`。 如果发生这种情况，请将 `--ssl-mode=REQUIRED --ssl-ca={filepath}` 参数替换为 `--ssl`。

## <a name="enforcing-ssl-connections-in-azure"></a>在 Azure 中强制实施 SSL 连接 
### <a name="using-the-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，访问 Azure Database for MariaDB 服务器，并单击“连接安全性”  。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置，并单击“保存”   。 Microsoft 建议始终启用“强制实施 SSL 连接”设置，以增强安全性****。
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用“Enabled”或“Disabled”值来启用或禁用“ssl-enforcement”参数  。
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>验证 SSL 连接
执行 mysql status 命令，验证是否已使用 SSL 连接到 MariaDB 服务器： 
```sql
status
```
查看输出以确认连接是否已加密，如果已加密，输出应显示为：“SSL:  使用中的密码为 AES256-SHA” 

## <a name="sample-code"></a>代码示例
若要从应用程序通过 SSL 与 Azure Database for MariaDB 建立安全连接，请参阅以下代码示例：

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on Rails
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
