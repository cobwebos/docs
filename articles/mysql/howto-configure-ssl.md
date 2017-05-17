---
title: "配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL | Microsoft Docs"
description: "介绍了如何正确配置 Azure Database for MySQL 和关联的应用程序，以正确使用 SSL 连接"
services: mysql
author: JasonMAnderson
ms.author: janders
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
ms.openlocfilehash: 96856fe2a9ce869eb63b7c857de614202ae43064
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL
Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

默认情况下，应将数据库服务配置为需要 SSL 连接才可连接到 Azure Database for MySQL 服务器。  建议尽量不要禁用 SSL 选项。 

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接
通过 Azure 门户或 CLI 预配新的 Azure Database for MySQL 服务器时，默认情况下会强制实施 SSL 连接。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-ssl"></a>配置强制实施 SSL
可以禁用或启用强制实施 SSL。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”设置，以增强安全性。

### <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，访问 Azure Database for MySQL 服务器，然后单击“连接安全性”。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置。 。 Microsoft 建议始终启用“强制实施 SSL 连接”设置，以增强安全性。 
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

可以通过在“概述”页中查看“SSL 强制实施状态”指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用“Enabled”或“Disabled”值来启用或禁用“ssl-enforcement”参数。
```azurecli
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接
许多将 MySQL 用于数据库服务的常见应用程序（例如 Wordpress、Drupal 和 Magento）在安装过程中不会默认启用 SSL。  必须在安装完毕后启用 SSL 连接性，或者通过特定于应用程序的 CLI 命令启用。  如果 MySQL 服务器强制实施了 SSL 连接，但是未正确配置关联的应用程序，那么应用程序可能无法连接到数据库服务器。  请查阅应用程序文档，了解如何启用 SSL 连接。

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>需要本地证书才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件 (.pem) 才能实现安全连接。  请按照以下步骤获取 .cer 文件，生成本地 .pem 文件，并将其绑定到应用程序。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>从证书颁发机构 (CA) 下载证书文件 
可在[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)找到通过 SSL 与 Azure Database for MySQL 服务器通信所需的证书。  将证书文件下载到本地驱动器（本教程中使用的是 c:\ssl）。

### <a name="download-and-install-openssl-on-your-pc"></a>下载 OpenSSL 并将其安装到电脑上 
若要生成应用程序安全连接到数据库服务器所需的本地 .pem 文件，需要在本地计算机上安装 OpenSSL。  

以下部分介绍了使用 Linux 和 Windows 电脑执行相关操作的方法，用户只需根据选择的操作系统按照其中一种方法执行操作即可。

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Linux 用户 - 使用 Linux 电脑下载并安装 OpenSSL
[OpenSSL Software Foundation](http://www.openssl.org) 的源代码中直接提供了 OpenSSL 库。  以下说明将指导用户完成在 Linux 电脑上安装 OpenSSL 所需的步骤。  本指南将演示用于 Ubuntu 12.04 和更高版本的命令。

打开终端会话并安装 OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
将下载的程序包中的文件解压缩
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
输入存储解压缩文件的目录。  默认情况下，应如下所示。

```bash
cd openssl-1.1.0e
```
通过执行以下命令配置 OpenSSL：如果所需文件保存在除 /usr/local/openssl 之外的其他文件夹中，请务必对以下内容进行相应的更改。

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
正确配置 OpenSSL 之后，需要对其进行编译，实现证书转换。  若要进行编译，请运行以下命令：

```bash
make
```
编译完成后，即可通过运行以下命令将 OpenSSL 安装为可执行文件：
```bash
make install
```
若要确认已成功在系统上安装 OpenSSL，请运行以下命令并进行检查，确保获得相同的输出。

```bash
/usr/local/openssl/bin/openssl version
```
如果成功，则应看到以下消息：
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Windows 电脑用户 - 使用 Windows 电脑下载并安装 OpenSSL
若要生成应用程序安全连接到数据库服务器所需的本地 .pem 文件，需要在本地计算机上安装 OpenSSL。

[OpenSSL Software Foundation](http://www.openssl.org) 的源代码中直接提供了 OpenSSL 库。 以下说明将指导用户完成在 Linux 电脑上安装 OpenSSL 所需的步骤。

可通过以下方式在 Windows 电脑上安装 OpenSSL：

1. （推荐）使用 Window 10 和更高版本中内置的 Bash for Windows 功能，已默认安装 OpenSSL。  可在[此处](https://msdn.microsoft.com/commandline/wsl/install_guide)了解如何在 Windows 10 中启用 Bash for Windows 功能。

2. 通过下载社区提供的 Win32/64 应用程序。 虽然 OpenSSL Software Foundation 并未提供或赞同任何特定的 Windows 安装程序，但在[此处](https://wiki.openssl.org/index.php/Binaries)提供了可用安装程序列表

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>将 .cer 证书转换为本地 .pem
下载的根 CA 文件是 .cer 格式。 需要使用 OpenSSL 将证书文件转换为 .pem 格式。  为此，请执行 openssl.exe 命令行工具和以下命令： 

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -out MyServerCACert.pem
```
成功创建证书文件 (MyServerCACert.pem) 之后，即可通过 SSL 安全地连接到数据库服务器。 

以下示例演示了如何使用 MyServerCACert.pem 文件，通过 MySQL 命令行接口和 MySQL Workbench 连接到 MySQL 服务器。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 通过 SSL 连接到服务器
使用 MySQL 命令行接口，执行以下命令：

```dos
mysql.exe -h yourserver. -uUsername@Servername -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
执行 mysql status 命令，验证是否已使用 SSL 连接到 MySQL 服务器：

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 通过 SSL 连接到服务器
要将 MySQL Workbench 配置为通过 SSL 安全地连接，需要导航到 MySQL Workbench“设置新连接”对话框中的“SSL”选项卡，然后在“SSL CA 文件:”字段中输入 MyServerCACert.pem 文件的位置。
![保存自定义磁贴](./media/concepts-ssl-connection-security/mysql-workbench-ssl.png)

## <a name="next-steps"></a>后续步骤
- 在 [Connection libraries for Azure Database for MySQL](concepts-connection-libraries.md)（Azure Database for MySQL 的连接库）中查看各种应用程序连接性选项
