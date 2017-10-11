---
title: "配置 Azure Database for PostgreSQL 中的 SSL 连接 | Microsoft Docs"
description: "有关如何配置 Azure Database for PostgreSQL 和关联应用程序以正确使用 SSL 连接的说明和信息。"
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 05/15/2017
ms.openlocfilehash: 685aa4c2f75b7c3260ca737f7c786157480b2d90
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL 中的 SSL 连接
Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 SSL 连接。 （可选）如果客户端应用程序不支持 SSL 连接，则可以禁止要求 SSL 连接到数据库服务。 

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接
对于通过 Azure 门户或 CLI 预配的所有 Azure Database for PostgreSQL 服务器，强制实施 SSL 连接是默认启用的。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-ssl"></a>配置强制实施 SSL
（可选）可以禁用强制实施 SSL 连接。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”设置，以增强安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 门户
访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置。 然后单击“保存”。 

![连接安全性 - 禁用强制实施 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

可以通过在“概述”页中查看“SSL 强制实施状态”指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接
许多将 PostgreSQL 用于数据库服务的常见应用程序框架（例如Drupal 和 Django）在安装过程中不会默认启用 SSL。 必须在安装完毕后启用 SSL 连接性，或者通过特定于应用程序的 CLI 命令启用。 如果 PostgreSQL 服务器强制实施了 SSL 连接，但是未正确配置关联的应用程序，那么应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 SSL 连接。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要证书验证才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 请参阅以下步骤获取 .cer 文件，解码证书并将其绑定到应用程序。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>从证书颁发机构 (CA) 下载证书文件 
可在[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)找到通过 SSL 与 Azure Database for PostgreSQL 服务器通信所需的证书。 本地下载证书文件。

### <a name="download-and-install-openssl-on-your-machine"></a>在计算机上下载并安装 OpenSSL 
若要解码应用程序安全连接到数据库服务器所需的本地证书文件，需要在本地计算机上安装 OpenSSL。

#### <a name="for-linux-os-x-or-unix"></a>适用于 Linux、OS X 或 Unix
[OpenSSL Software Foundation](http://www.openssl.org) 的源代码中直接提供了 OpenSSL 库。 以下说明将指导用户完成在 Linux 电脑上安装 OpenSSL 所需的步骤。 本文使用适用于 Ubuntu 12.04 及更高版本的命令。

打开终端会话并安装 OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
将下载的程序包中的文件解压缩
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
输入存储解压缩文件的目录。 默认情况下，应如下所示。

```bash
cd openssl-1.1.0e
```
通过执行以下命令配置 OpenSSL。 如果所需文件保存在除 /usr/local/openssl 之外的其他文件夹中，请务必对以下内容进行相应的更改。

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
正确配置 OpenSSL 之后，需要对其进行编译，以转换证书。 若要进行编译，请运行以下命令：

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
如果成功，则应看到以下消息。
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>对于 Windows
可通过以下方式在 Windows 电脑上安装 OpenSSL：
1. （推荐）使用 Window 10 和更高版本中内置的 Bash for Windows 功能，已默认安装 OpenSSL。 可在[此处](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)了解如何在 Windows 10 中启用 Bash for Windows 功能。
2. 通过下载社区提供的 Win32/64 应用程序。 虽然 OpenSSL Software Foundation 并未提供或赞同任何特定的 Windows 安装程序，但在[此处](https://wiki.openssl.org/index.php/Binaries)提供了可用安装程序列表

### <a name="decode-your-certificate-file"></a>解码证书文件
下载的根 CA 文件采用加密格式。 使用 OpenSSL 解码证书文件。 要执行此操作，请运行此 OpenSSL 命令：

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>连接到具有 SSL 证书身份验证的 Azure Database for PostgreSQL
现已成功解码证书，可通过 SSL 安全连接到数据库服务器。 要允许服务器证书验证，必须将证书放在用户主目录中的 ~/.postgresql/root.crt 文件中。 （在 Microsoft Windows 上，该文件的名称是 %APPDATA%\postgresql\root.crt.）。 下面说明如何连接到 Azure Database for PostgreSQL。

> [!NOTE]
> 目前，没有已知的问题如果你使用"sslmode = 验证完整"在你连接到服务中，则连接将失败并出现以下错误：_服务器证书"&lt;区域&gt;。 control.database.windows.net"（和 7 其他名称） 与主机名不匹配"&lt;servername&gt;。 postgres.database.azure.com"。_
> 如果需要“sslmode=verify-full”，请使用服务器命名约定 **&lt;servername&gt;.database.windows.net** 作为连接字符串主机名。 我们计划在将来删除此限制。 使用其他 [SSL 模式](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS)的连接应继续使用首选主机命名约定 **&lt;servername&gt;.postgres.database.azure.com**。

#### <a name="using-psql-command-line-utility"></a>使用 psql 命令行实用工具
以下示例演示如何使用 psql 命令行实用程序成功连接到 PostgreSQL 服务器。 使用创建的 `root.crt` 文件和 `sslmode=verify-ca` 或 `sslmode=verify-full` 选项。

使用 PostgreSQL 命令行接口执行以下命令：
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
如果成功，则会收到以下输出：
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>使用 pgAdmin GUI 工具
要将 pgAdmin 4 配置为通过 SSL 安全连接，需要对 `SSL mode = Verify-CA` 或 `SSL mode = Verify-Full` 进行如下设置：

![pgAdmin - 连接 - SSL 模式要求的屏幕快照](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>后续步骤
在 [Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md)（Azure Database for PostgreSQL 的连接库）中查看各种应用程序连接性选项
