---
title: "使用 Go 语言连接到 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入门提供了一个 Go 编程语言示例，你可以使用它来连接到 Azure Database for PostgreSQL 并查询其中的数据。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---

# Azure Database for PostgreSQL：使用 Go 语言进行连接并查询数据
<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>
本快速入门演示了如何使用以 [Go](https://golang.org/) 语言编写的代码连接到 Azure Database for PostgreSQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本文假设你熟悉如何使用 Go 进行开发，但不熟悉如何使用 Azure Database for PostgreSQL。

## 先决条件
<a id="prerequisites" class="xliff"></a>
此快速入门使用以下任意指南中创建的资源作为起点：
- [创建 DB - 门户](quickstart-create-server-database-portal.md)
- [创建 DB - Azure CLI](quickstart-create-server-database-azure-cli.md)

## 安装 Go 和 pq
<a id="install-go-and-pq" class="xliff"></a>
- 下载 Go，然后根据与平台相对应的[安装说明](https://golang.org/doc/install)进行安装。
- 为项目创建一个文件夹，例如 C:\Postgresql\。 使用命令行将目录更改成项目文件夹，例如 `cd C:\Postgres\`。
- 将[纯 Go 语言 postgres 驱动程序 (pq)](https://github.com/lib/pq) 下载到项目文件夹中，方法是在同一目录中键入命令 `go get github.com/lib/pq`。

## 生成和运行 Go 代码
<a id="build-and-run-go-code" class="xliff"></a> 
- 将代码保存到扩展名为 *.go 的文本文件中，然后将该文件保存到项目文件夹（例如 `C:\postgres\read.go`）中。
- 若要运行代码，请将目录更改为项目文件夹 `cd postgres`，然后键入命令 `go run read.go` 来编译并运行应用程序。
- 若要将代码构建成本机应用程序中，请 `go build read.go`，然后启动 read.exe 来运行该应用程序。

## 获取连接信息
<a id="get-connection-information" class="xliff"></a>
获取连接到 Azure Database for PostgreSQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户中的左侧菜单中，单击“所有资源”，然后搜索已创建的服务器，例如 **mypgserver-20170401**。
3. 单击服务器名称 **mypgserver-20170401**。
4. 选择服务器的“概述”页面。 记下“服务器名称”和“服务器管理员登录名”。
 ![Azure Database for PostgreSQL - 服务器管理员登录名](./media/connect-go/1-connection-string.png)
5. 如果忘记了服务器登录信息，请导航到“概览”页，然后查看服务器管理员登录名。 如有必要，请重置密码。

## 进行连接并创建表
<a id="connect-and-create-a-table" class="xliff"></a>
使用以下代码进行连接，使用 **CREATE TABLE** SQL 语句创建表，然后使用 **INSERT INTO** SQL 语句将行添加到表中。

代码导入三个包：[sql 包](https://golang.org/pkg/database/sql/)、[pq 包](http://godoc.org/github.com/lib/pq)（充当与 Postgres 服务器通信所需的驱动程序）以及 [fmt 包](https://golang.org/pkg/fmt/)（适用于在命令行中列显的输入和输出）。

代码通过调用 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 方法来连接到 Azure Database for PostgreSQL，并使用 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 方法来检查连接。 将始终使用[数据库句柄](https://golang.org/pkg/database/sql/#DB)来存储数据库服务器的连接池。 代码多次调用 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 方法来运行多个 SQL 命令。 每次都会使用自定义 checkError() 方法来检查是否发生错误，以及是否在发生错误时死机退出。


将 `HOST`、`DATABASE`、`USER` 和 `PASSWORD` 参数替换为你自己的值。 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## 读取数据
<a id="read-data" class="xliff"></a>
使用以下代码进行连接，并使用 **SELECT** SQL 语句来读取数据。 

代码导入三个包：[sql 包](https://golang.org/pkg/database/sql/)、[pq 包](http://godoc.org/github.com/lib/pq)（充当与 Postgres 服务器通信所需的驱动程序）以及 [fmt 包](https://golang.org/pkg/fmt/)（适用于在命令行中列显的输入和输出）。

代码通过调用 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 方法来连接到 Azure Database for PostgreSQL，并使用 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 方法来检查连接。 将始终使用[数据库句柄](https://golang.org/pkg/database/sql/#DB)来存储数据库服务器的连接池。 select 查询通过调用方法 [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) 来运行，生成的行保留在类型为[行](https://golang.org/pkg/database/sql/#Rows)的变量中。 代码使用 [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) 方法来读取当前行中的列数据值，并使用迭代器 [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) 来循环访问相关行，直到再也没有行存在。 每行的列值列显到控制台。 每次都会使用自定义 checkError() 方法来检查是否发生错误，以及是否在发生错误时死机退出。

将 `HOST`、`DATABASE`、`USER` 和 `PASSWORD` 参数替换为你自己的值。 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## 更新数据
<a id="update-data" class="xliff"></a>
使用以下代码进行连接，并使用 **UPDATE** SQL 语句更新数据。

代码导入三个包：[sql 包](https://golang.org/pkg/database/sql/)、[pq 包](http://godoc.org/github.com/lib/pq)（充当与 Postgres 服务器通信所需的驱动程序）以及 [fmt 包](https://golang.org/pkg/fmt/)（适用于在命令行中列显的输入和输出）。

代码通过调用 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 方法来连接到 Azure Database for PostgreSQL，并使用 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 方法来检查连接。 将始终使用[数据库句柄](https://golang.org/pkg/database/sql/#DB)来存储数据库服务器的连接池。 代码通过调用 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 方法来运行 SQL 语句，以便更新表。 一个自定义的 checkError() 方法，用于检查是否发生错误，以及在发生错误时是否死机退出。

将 `HOST`、`DATABASE`、`USER` 和 `PASSWORD` 参数替换为你自己的值。 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## 删除数据
<a id="delete-data" class="xliff"></a>
使用以下代码进行连接，并使用 **DELETE** SQL 语句读取数据。 

代码导入三个包：[sql 包](https://golang.org/pkg/database/sql/)、[pq 包](http://godoc.org/github.com/lib/pq)（充当与 Postgres 服务器通信所需的驱动程序）以及 [fmt 包](https://golang.org/pkg/fmt/)（适用于在命令行中列显的输入和输出）。

代码通过调用 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 方法来连接到 Azure Database for PostgreSQL，并使用 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 方法来检查连接。 将始终使用[数据库句柄](https://golang.org/pkg/database/sql/#DB)来存储数据库服务器的连接池。 代码通过调用 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 方法来运行 SQL 语句，以便更新表。 一个自定义的 checkError() 方法，用于检查是否发生错误，以及在发生错误时是否死机退出。

将 `HOST`、`DATABASE`、`USER` 和 `PASSWORD` 参数替换为你自己的值。 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## 后续步骤
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md)

