---
title: 在 Linux VM 上设置 PostgreSQL
description: 了解如何在 Azure 中的 Linux 虚拟机上安装和配置 PostgreSQL
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: fdde7613627c9fec0694f3985f78cf10e52f59c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397090"
---
# <a name="install-and-configure-postgresql-on-azure"></a>在 Azure 上安装和配置 PostgreSQL
PostgreSQL 是一种类似于 Oracle 和 DB2 的高级开放源代码数据库。 它包含许多企业级功能，比如完整的 ACID 合规性、可靠的事务处理和多版本并发控制。 它还支持 ANSI SQL 和 SQL/MED（包括 Oracle、MySQL、MongoDB 等等的外来数据包装器）等标准。 它具有高度的可扩展性，支持超过 12 种程序语言，并支持 GIN 和 GiST 索引、空间数据，以及面向 JSON 或基于键值的应用程序的多款类似于 NoSQL 的功能。

在本文中，你将了解如何在运行 Linux 的 Azure 虚拟机上安装和配置 PostgreSQL。


## <a name="install-postgresql"></a>安装 PostgreSQL
> [!NOTE]
> 你必须已经有一个运行 Linux 的 Azure 虚拟机，才能完成本教程。 在继续操作前，若要创建并设置 Linux VM，请参阅 [Azure Linux VM 教程](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> 
> 

在此示例中，使用端口 1999 作为 PostgreSQL 端口。  

通过 PuTTY 连接到你创建的 Linux VM。 如果这是你首次使用 Azure Linux VM，请参阅[如何在 Azure 上将 SSH 用于 Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，了解如何使用 PuTTY 连接到 Linux VM。

1. 运行以下命令切换成根用户（管理员）：

    ```console
    # sudo su -
    ```

2. 某些发行版具有在安装 PostgreSQL 之前必须安装的依赖项。 查看此列表中的发行版并运行相应的命令：
   
   * 基于 Red Hat 的 Linux：

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * 基于 Debian 的 Linux：

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * SUSE Linux：

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. 将 PostgreSQL 下载到根目录中，然后对包进行解压缩：

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    上面是一个示例： 可以在 [/pub/source/ 的索引](https://ftp.postgresql.org/pub/source/)中找到更详细的下载地址。

4. 若要启动生成，请运行这些命令：

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. 如果希望生成所有能生成的内容，包括文档（HTML 和手册页）和其他模块 (`contrib`)，则改为运行以下命令：

    ```console
    # gmake install-world
    ```

    你应该会收到以下确认消息：

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>配置 PostgreSQL
1. （可选）创建一个符号链接以便将 PostgreSQL 引用缩短为不包含版本号：

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. 为数据库创建一个目录：

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. 创建一个非根用户并修改该用户的配置文件。 然后，切换到该新用户（本例中称为 *postgres*）：

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > 出于安全原因，PostgreSQL 将使用非根用户来初始化、启动或关闭数据库。
   > 
   > 
4. 通过输入以下命令编辑 *bash_profile* 文件。 这些行将添加到 *bash_profile* 文件的末尾：

    ```config
    cat >> ~/.bash_profile <<EOF
    export PGPORT=1999
    export PGDATA=/opt/pgsql_data
    export LANG=en_US.utf8
    export PGHOME=/opt/pgsql
    export PATH=\$PATH:\$PGHOME/bin
    export MANPATH=\$MANPATH:\$PGHOME/share/man
    export DATA=`date +"%Y%m%d%H%M"`
    export PGUSER=postgres
    alias rm='rm -i'
    alias ll='ls -lh'
    EOF
    ```

5. 执行 *bash_profile* 文件：

    ```console
    $ source .bash_profile
    ```

6. 使用以下命令验证你的安装：

    ```console
    $ which psql
    ```

    如果安装成功，你将看到以下响应：

    ```output
    /opt/pgsql/bin/psql
    ```

7. 你还可以检查 PostgreSQL 版本：

    ```sql
    $ psql -V
    ```

8. 初始化数据库：

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    你应该会收到以下输出：

![在初始化数据库后显示输出的屏幕截图。](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>设置 PostgreSQL
<!--    [postgres@ test ~]$ exit -->

运行以下命令：

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

修改 /etc/init.d/postgresql 文件中的两个变量。 前缀设置为 PostgreSQL 的安装路径： **/opt/pgsql**。 PGDATA 设置为 PostgreSQL 的数据存储路径： **/opt/pgsql_data**。

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![屏幕截图，显示安装前缀和数据目录。](./media/postgresql-install/no2.png)

更改文件以使其成为可执行文件：

```console
# chmod +x /etc/init.d/postgresql
```

启动 PostgreSQL：

```console
# /etc/init.d/postgresql start
```

检查 PostgreSQL 的终结点是否已打开：

```console
# netstat -tunlp|grep 1999
```

你应该会看到以下输出：

![显示 PostgreSQL 终结点的屏幕截图。](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>连接到 Postgres 数据库
再次切换成 postgres 用户：

```console
# su - postgres
```

创建一个 Postgres 数据库：

```console
$ createdb events
```

连接到刚创建的事件数据库：

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>创建和删除 Postgres 表
现在，你已经连接到数据库，可以在其中创建表了。

例如，使用以下命令创建一个新的示例 Postgres 表：

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

现在已经设置一个具有以下列名和限制的 4 列表：

1. “name”列受 VARCHAR 命令限制，必须少于 20 个字符。
2. “food”列指示每个人将携带的食物。 VARCHAR 将此文本限制为 30 个字符以下。
3. “confirmed”列记录此人是否接受了百乐餐邀请。 可接受的值为“Y”和“N”。
4. “date”列会在他们报名参加该活动时显示。 Postgres 要求日期格式为 yyyy-mm-dd。

如果成功创建了表，你应该会看到以下内容：

![显示成功创建表时显示的消息的屏幕截图。](./media/postgresql-install/no4.png)

你还可以使用以下命令检查表结构：

![显示用于检查表结构的命令的屏幕截图。](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>向表中添加数据
首先，将信息插入某一行：

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

你应该会看到以下输出：

![屏幕截图，显示您添加的行信息。](./media/postgresql-install/no6.png)

你也可以向表中多添加几个人。 下面是几个选项，你也可以自行创建：

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>显示表
使用以下命令来显示表：

```sql
select * from potluck;
```

输出为：

![显示用于显示表的命令输出的屏幕截图。](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>删除表中的数据
使用以下命令来删除表中的数据：

```sql
delete from potluck where name=’John’;
```

这将删除“John”行中的所有信息。 输出为：

![图像](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>更新表中的数据
使用以下命令来更新表中的数据。 在此示例中，Sandy 已经确认将参加该活动，因此我们将 RSVP 从“N”更改为“Y”：

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>获取有关 PostgreSQL 的详细信息
现在，你已完成在 Azure Linux VM 中安装 PostgreSQL，你可以在 Azure 中享受使用它的过程。 若要了解有关 PostgreSQL 的详细信息，请访问 [PostgreSQL 网站](https://www.postgresql.org/)。
