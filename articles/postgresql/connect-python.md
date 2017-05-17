---
title: "Python 从 Azure Database for PostgreSQL 读取 | Microsoft Docs"
description: "在假定读者不具备任何相关知识的前提下，介绍如何运行 Python 代码示例以在 Azure Database for PostgreSQL 中的表中写入和读取数据。"
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Python 从 Azure Database for PostgreSQL 读取


本文提供一个简短 Python 程序，该程序使用 SQL SELECT 语句从表中读取一行。 文章还介绍如何查找和安装运行 Python 程序所需的所有系统必备组件。

文中介绍的示例 Python 程序和 Python 相关工具适用于各种平台，包括 Linux、Mac 和 Windows。 


## <a name="install-the-python-interpreter"></a>安装 Python 解释程序


本文中编写的 Python 代码示例适用于Python 解释程序 2.7 版，不能用 3.x 版运行。

从以下位置下载并安装 Python 解释程序 2.7 版：

- [从 python.org 下载 Python 解释程序](https://www.python.org/downloads/)

安装后，请验证能在命令行上找到并运行该解释程序。 使用如下命令：

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>安装 pip.exe - Python 模块安装程序


安装 Python 解释程序可能还会在名为“Scripts/”的子目录下安装 pip.exe。 pip.exe 安装专用的 Python 模块。 验证能找到并运行 pip.exe。

`pip.exe`

如果无法运行 pip.exe，请查看是否具有名为“get pip.py”的 Python 实用工具程序文件。 如果有 get-pip.py，则可以运行它来获取 pip.exe：

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>安装 psycopg - 连接模块


Python 程序需要一个知道如何将程序连接到 Azure Database for PostgreSQL 服务器的模块。 连接模块的名称是“psycopg2”。 如需更多详细信息，请参阅：

- [psycopg2 网站](http://initd.org/psycopg/)

使用以下 pip.exe 安装命令安装 psycopg2：

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器


如果尚不具有访问 Azure Database for PostgreSQL 服务器的权限，可借助下面的文档了解创建服务器的方法：

- [使用 Azure 门户创建 Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)
- [使用 Azure CLI 创建 Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>获取连接字符串值


可从 Azure 门户中获取 Azure Database for PostgreSQL 服务器的连接字符串值。 需要下表中提供的参数值。

- “名称”列 &ndash; 显示 psycopg2 所需的参数标识符。
- “-Symbol”列 &ndash; 显示示例 Python 程序 PythonDriver.py 所需的参数标识符。


| 名称 | -Symbol | 值示例 |
| :--  | :--     | :--           |
| 主机 | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />（所有 PostgreSQL 服务器都具有一个名为“postgres”的数据库。）** |
| 端口 | -p | 5432（可能是特定值 5432。） |
| password | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Python 示例程序


本部分提供示例 Python 程序的源代码。 将在本文的后面部分中运行该程序。

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>用于运行示例程序的命令行


本部分显示用于测试 Python 示例程序的命令行，同时显示实际确认输出。

对于前面所述的参数，必须将此处显示的示例值替换为实际连接字符串值。

用于运行前面的示例 Python 程序的具体语法可能略有不同。 具体语法取决于操作系统和使用的控制台类型。


#### <a name="windows-cmdexe-console"></a>Windows cmd.exe 控制台


以下代码块显示示例 Python 程序的实际测试运行。 使用了简单 cmd.exe 命令行。 对每个“^”续行符进行类型化后：

1. 按 Enter 键。
2. 显示短语“More?”。
3. 对整行的另一部分进行类型化，以此类推。

此续行方法用于防止示例过宽，避免在此处或打印时出现显示效果较差的情况。

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

可查看“SUCCESS”行确认程序已运行。

还可以将整个命令行放入 .bat 文件来实现此效果。 然后便可从 cmd.exe 命令行中运行 .bat 文件。


#### <a name="powershell-file"></a>PowerShell 文件


在 PowerShell 控制台中，命令行不支持续行符。 因此在此 PowerShell 部分中，将命令放入了 PowerShell 文件。 然后从 PowerShell 命令行中运行该文件。

将以下代码复制到名为“PythonDriverRun.ps1”的文件中。

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

运行 PythonDriverRun.ps1，如下所示。 可查看程序打印的“SUCCESS”行以进行确认。

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

如果想要直接在 PowerShell 命令行上运行 PythonDriverRun.ps1，则必须在前面添加一个 & 字符和一个空格。 如果不以 & 开头，确认消息会快速消失，无法查看。

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>安装 pgAdmin，用于检查服务器


PythonDriverRun.ps1 程序结束时，它会通过删除程序创建的 testpy1 表实现自行清除。 可以选择使用“#”注释掉发出 DROP TABLE 语句的源代码行。 此选项会保留表，以便可在以后检查表。

使用 pgAdmin 工具可检查任何 PostgreSQL 服务器及服务器中的对象。 Microsoft SQL Server 或 Azure SQL 数据库用户会发现 SQL Server Management Studio (SSMS) 和 pgAdmin 的相似之处。

如果需要，可安装 pgAdmin 来检查服务器和 testpy1 表。


#### <a name="1-install-pgadmin"></a>1.安装 pgAdmin


有关 pgAdmin 的安装说明，请访问：

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

**pgAdmin4.exe** 可能是可执行文件的名称，而不是简单地采用 pgAdmin.exe。

若要在 Windows 计算机上运行 pgAdmin4.exe，请在命令行上输入类似以下命令的内容：

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2.将 pgAdmin 连接到服务器

 
pgAdmin 用户界面显示后，找到“浏览器”窗格。 然后右键单击“服务器” > “创建” > “服务器”。 此处的“创建”指创建与任何现有 PostgreSQL 服务器（包括任何 Azure Database for PostgreSQL 服务器）的连接。

连接建立后，“浏览器”窗格中将显示对象树。


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3.在 pgAdmin 树中导航到自己的表


若要查看表，请展开树元素，如下所示：

- 服务器 &gt; [你的服务器] &gt; 数据库 &gt; postgres &gt; 架构 &gt; 公用 &gt; 表 &gt; testpy1

![pgAdmin，用于在树中显示表 testpy1](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4.删除 testpy1 表


对于最后清理，请右键单击 testpy1 节点，然后单击“删除”。


## <a name="next-steps"></a>后续步骤

- [Python 连接到 Azure SQL 数据库](../sql-database/sql-database-connect-query-python.md)
- [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)

