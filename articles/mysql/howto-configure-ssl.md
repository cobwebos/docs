---
title: "配置 SSL 连接性以安全连接到 Azure Database for MySQL | Microsoft Docs"
description: "介绍了如何正确配置 Azure Database for MySQL 和关联的应用程序，以正确使用 SSL 连接"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 079bb22aa76b8354f79400ced4e04dc971ea249a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL
Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

## <a name="step-1-obtain-ssl-certificate"></a>步骤 1：获取 SSL 证书
从 [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 下载通过 SSL 与 Azure Database for MySQL 服务器通信所需的证书，然后将证书文件保存到本地驱动器（在本教程中，我们使用 c:\ssl）。
**对于 Microsoft Internet Explorer 和 Microsoft Edge：**下载完成之后，将证书重命名为 BaltimoreCyberTrustRoot.crt.pem。

## <a name="step-2-bind-ssl"></a>步骤 2：绑定 SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 通过 SSL 连接到服务器
配置 MySQL Workbench，以便安全地通过 SSL 连接。 从“设置新连接”对话框，导航到“SSL”选项卡。在“SSL CA 文件:”字段中输入 BaltimoreCyberTrustRoot.crt.pem 的文件位置。 
![保存自定义磁贴](./media/howto-configure-ssl/mysql-workbench-ssl.png) 对于现有连接，可以通过右键单击“连接”图标并选择“编辑”来绑定 SSL。 然后导航到“SSL”选项卡，并绑定证书文件。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 通过 SSL 连接到服务器
绑定 SSL 证书的另一种方法是使用 MySQL 命令行接口执行以下命令：
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>步骤 3：在 Azure 中强制实施 SSL 连接 
### <a name="using-the-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，访问 Azure Database for MySQL 服务器，并单击“连接安全性”。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置，并单击“保存”。 Microsoft 建议始终启用“强制实施 SSL 连接”设置，以增强安全性。
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用“Enabled”或“Disabled”值来启用或禁用“ssl-enforcement”参数。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>步骤 4：验证 SSL 连接
执行 mysql status 命令，验证是否已使用 SSL 连接到 MySQL 服务器：
```dos
mysql> status
```
通过查看输出来确认连接是否已加密，如果已加密，输出应显示为：“SSL: 使用中的密码为 AES256-SHA” 

## <a name="sample-code"></a>代码示例
若要从应用程序通过 SSL 与 Azure Database for MySQL 建立安全连接，请参阅以下代码示例。
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>后续步骤
在 [Connection libraries for Azure Database for MySQL](concepts-connection-libraries.md)（Azure Database for MySQL 的连接库）中查看各种应用程序连接性选项
