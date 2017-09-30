---
title: "从 Node.js 连接到 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入门提供了一个 Node.js 代码示例，你可以使用它来连接到 Azure Database for PostgreSQL 并查询其中的数据。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2017
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: f6c98833c73b70bcf1f8ca53596a34f09807b276
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---

# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Azure Database for PostgreSQL：使用 Node.js 进行连接并查询数据
本快速入门演示了如何使用 [Node.js](https://nodejs.org/) 连接到 Azure Database for PostgreSQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本文中的步骤假定你熟悉如何使用 Node.js 进行开发，但不熟悉如何使用 Azure Database for PostgreSQL。

## <a name="prerequisites"></a>先决条件
此快速入门使用以下任意指南中创建的资源作为起点：
- [创建 DB - 门户](quickstart-create-server-database-portal.md)
- [创建 DB - CLI](quickstart-create-server-database-azure-cli.md)

还需要：
- 安装 [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>安装 pg 客户端
安装 [pg](https://www.npmjs.com/package/pg)，这是 Node.js 的 PostgreSQL 客户端。

为此，请从命令行运行适用于 JavaScript 的节点包管理器 (npm)，以便安装 pg 客户端。
```bash
npm install pg
```

通过列出已安装的包来验证安装。
```bash
npm list
```

## <a name="get-connection-information"></a>获取连接信息
获取连接到 Azure Database for PostgreSQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户中的左侧菜单中，单击“所有资源”，并搜索刚创建的服务器。
3. 单击服务器名称。
4. 选择服务器的“概述”页面。 记下“服务器名称”和“服务器管理员登录名”。
 ![Azure Database for PostgreSQL - 服务器管理员登录名](./media/connect-nodejs/1-connection-string.png)
5. 如果忘了服务器的登录信息，请导航到“概览”页，以查看服务器管理员登录名并重置密码（如果需要）。

## <a name="running-the-javascript-code-in-nodejs"></a>在 Node.js 中运行 JavaScript 代码
可以通过键入 `node` 从 bash shell 或 Windows 命令提示符启动 Node.js，然后以交互方式运行示例 JavaScript 代码（只需将其复制并粘贴到提示符处即可）。 也可将 JavaScript 代码保存到文本文件中，然后以文件名作为运行所需的参数来启动 `node filename.js`。

## <a name="connect-create-table-and-insert-data"></a>进行连接，创建表，然后插入数据
通过以下代码进行连接，然后使用 **CREATE TABLE** 和 **INSERT INTO** SQL 语句加载数据。
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>读取数据
使用以下代码进行连接，并使用 **SELECT** SQL 语句来读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>更新数据
使用以下代码进行连接，并使用 **UPDATE** SQL 语句读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>删除数据
使用以下代码进行连接，并使用 **DELETE** SQL 语句读取数据。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 对象用作 PostgreSQL 服务器的接口。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函数用来建立到服务器的连接。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 

将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md)

