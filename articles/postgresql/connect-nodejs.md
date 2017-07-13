---
title: "从 Node.js 连接到 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入门提供了一个 Node.js 代码示例，你可以使用它来连接到 Azure Database for PostgreSQL 并查询其中的数据。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---

# Azure Database for PostgreSQL：使用 Node.js 进行连接并查询数据
<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>
本快速入门演示了如何使用 Windows、Ubuntu Linux 和 Mac 平台的 [Node.js](https://nodejs.org/) 连接到 Azure Database for PostgreSQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本文中的步骤假定你熟悉如何使用 Node.js 进行开发，但不熟悉如何使用 Azure Database for PostgreSQL。

## 先决条件
<a id="prerequisites" class="xliff"></a>
此快速入门使用以下任意指南中创建的资源作为起点：
- [创建 DB - 门户](quickstart-create-server-database-portal.md)
- [创建 DB - CLI](quickstart-create-server-database-azure-cli.md)

你还需要：
- 安装 [Node.js](https://nodejs.org)
- 安装 [pg](https://www.npmjs.com/package/pg) 包。 

## 安装 Node.js
<a id="install-nodejs" class="xliff"></a> 
根据平台安装 Node.js：

### **Mac OS**
<a id="mac-os" class="xliff"></a>
输入以下命令安装 **brew**（适用于 Mac OS X 和 **Node.js** 的易用程序包管理器）。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### **Linux (Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
输入以下命令安装 **Node.js** 和 **npm**（适用于 Node.js 的程序包管理器）。

```bash
sudo apt-get install -y nodejs npm
```

### **Windows**
<a id="windows" class="xliff"></a>
请访问 [Node.js 下载页](https://nodejs.org/en/download/)并选择所需的 Windows 安装程序选项。

## 安装 pg 客户端
<a id="install-pg-client" class="xliff"></a>
安装 [pg](https://www.npmjs.com/package/pg)，这是 node.js 的纯 JavaScript 非阻止性客户端，适用于连接和查询 PostgreSQL。

为此，请从命令行运行适用于 JavaScript 的节点包管理器 (npm)，以便安装 pg 客户端。
```bash
npm install pg
```

通过列出已安装的包来验证安装。
```bash
npm list
```
列表命令输出确认每个组件的版本。 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

## 获取连接信息
<a id="get-connection-information" class="xliff"></a>
获取连接到 Azure Database for PostgreSQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户中的左侧菜单中，单击“所有资源”，然后搜索刚创建的服务器 **mypgserver-20170401**。
3. 单击服务器名称 **mypgserver-20170401**。
4. 选择服务器的“概述”页面。 记下“服务器名称”和“服务器管理员登录名”。
 ![Azure Database for PostgreSQL - 服务器管理员登录名](./media/connect-nodejs/1-connection-string.png)
5. 如果忘了服务器的登录信息，请导航到“概览”页，以查看服务器管理员登录名并重置密码（如果需要）。

## 在 Node.js 中运行 JavaScript 代码
<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>
可以通过键入 `node` 从 bash shell 或 Windows 命令提示符启动 Node.js，然后以交互方式运行示例 JavaScript 代码（只需将其复制并粘贴到提示符处即可）。 也可将 JavaScript 代码保存到文本文件中，然后以文件名作为运行所需的参数来启动 `node filename.js`。

## 进行连接，创建表，然后插入数据
<a id="connect-create-table-and-insert-data" class="xliff"></a>
通过以下代码进行连接，然后使用 **CREATE TABLE** 和 **INSERT INTO** SQL 语句加载数据。
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

## 读取数据
<a id="read-data" class="xliff"></a>
使用以下代码进行连接，并使用 **SELECT** SQL 语句来读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

## 更新数据
<a id="update-data" class="xliff"></a>
使用以下代码进行连接，并使用 **UPDATE** SQL 语句读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

## 删除数据
<a id="delete-data" class="xliff"></a>
使用以下代码进行连接，并使用 **DELETE** SQL 语句读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

## 后续步骤
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md)

