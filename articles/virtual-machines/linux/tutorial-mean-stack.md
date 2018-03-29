---
title: 在 Azure 中的 Linux VM 上创建 MEAN 堆栈 | Microsoft Docs
description: 了解如何在 Azure 中的 Linux VM 上创建 MongoDB、Express、AngularJS 和 Node.js (MEAN) 堆栈。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2bd89bf25f619caef07ae099232add55dbe0cda7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-vm-in-azure"></a>在 Azure 中的 Linux VM 上创建 MongoDB、Express、AngularJS 和 Node.js (MEAN) 堆栈

本教程介绍如何在 Azure 中的 Linux VM 上实现 MongoDB、Express、AngularJS 和 Node.js (MEAN) 堆栈。 通过创建的 MEAN 堆栈，可以在数据库中添加、删除和列出书籍。 学习如何：

> [!div class="checklist"]
> * 创建 Linux VM
> * 安装 Node.js
> * 安装 MongoDB 并设置服务器
> * 安装 Express 并设置服务器的路由
> * 使用 AngularJS 访问路由
> * 运行应用程序

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。


## <a name="create-a-linux-vm"></a>创建 Linux VM

使用 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 命令创建资源组，并使用 [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) 命令创建 Linux VM。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例使用 Azure CLI 在 *eastus* 位置创建名为 *myResourceGroupMEAN* 的资源组。 将会创建一个包含 SSH 密钥（如果默认密钥位置不存在这些密钥）的、名为 *myVM* 的 VM。 若要使用一组特定的密钥，请使用 --ssh-key-value 选项。

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息： 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
记下 `publicIpAddress`。 此地址用于访问 VM。

使用以下命令来与 VM 建立 SSH 会话。 请确保使用正确的公共 IP 地址。 在上例中，我们的 IP 地址为 13.72.77.9。

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>安装 Node.js

[Node.js](https://nodejs.org/en/) 是构建在 Chrome V8 JavaScript 引擎基础之上的 JavaScript 运行时。 本教程使用 Node.js 来设置 Express 路由和 AngularJS 控制器。

在 VM 上，使用通过 SSH 打开的 bash shell 安装 Node.js。

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>安装 MongoDB 并设置服务器
[MongoDB](http://www.mongodb.com) 将数据存储在灵活的、类似于 JSON 的文档中。 数据库中的字段根据文档的不同而异，数据结构随时可发生变化。 针对示例应用程序，我们要将包含书名、ISBN 编号、作者和页数的书籍记录添加到 MongoDB。 

1. 在 VM 上，使用通过 SSH 打开的 bash shell 设置 MongoDB 密钥。

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. 使用密钥更新包管理器。
  
    ```bash
    sudo apt-get update
    ```

3. 安装 MongoDB。

    ```bash
    sudo apt-get install -y mongodb
    ```

4. 启动服务器。

    ```bash
    sudo service mongodb start
    ```

5. 还需要安装 [body-parser](https://www.npmjs.com/package/body-parser-json) 包，以帮助我们处理在请求中传递给服务器的 JSON。

    安装 npm 包管理器。

    ```bash
    sudo apt-get install npm
    ```

    安装正文分析器包。
    
    ```bash
    sudo npm install body-parser
    ```

6. 创建名为 *Books* 的文件夹，并将包含 Web 服务器配置的、名为 *server.js* 的文件添加到该文件夹。

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>安装 Express 并设置服务器的路由

[Express](https://expressjs.com) 是一个微型的灵活 Node.js Web 应用程序框架，为 Web 和移动应用程序提供功能。 本教程使用 Express 将书籍信息传入和传出 MongoDB 数据库。 [Mongoose](http://mongoosejs.com) 提供简洁的基于架构的解决方案来为应用程序数据建模。 本教程使用 Mongoose 来为数据库提供书籍架构。

1. 安装 Express 和 Mongoose。

    ```bash
    sudo npm install express mongoose
    ```

2. 在 *Books* 文件夹中，创建名为 *apps* 的文件夹，并添加包含所定义的 Express 路由的、名为 *routes.js* 的文件。

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. 在 *apps* 文件夹中，创建名为 *models* 的文件夹，并添加包含所定义的书籍模型配置的、名为 *book.js* 的文件。  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>使用 AngularJS 访问路由

[AngularJS](https://angularjs.org) 提供一个 Web 框架用于在 Web 应用程序中创建动态视图。 本教程使用 AngularJS 将网页与 Express 相连接，并针对书籍数据库执行操作。

1. 将目录切换回到 *Books* (`cd ../..`)，然后创建名为 *public* 的文件夹，并添加包含所定义的控制器配置的、名为 *script.js* 的文件。

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. 在 *public* 文件夹中，创建包含所定义的网页的、名为 *index.html* 的文件。

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>运行应用程序

1. 将目录切换回到 *Books* (`cd ..`)，并通过运行以下命令启动服务器：

    ```bash
    nodejs server.js
    ```

2. 打开 Web 浏览器并导航到针对 VM 记录的地址。 例如 *http://13.72.77.9:3300*。 应显示以下页面所示的内容：

    ![书籍记录](media/tutorial-mean/meanstack-init.png)

3. 在文本框中输入数据并单击“添加”。 例如：

    ![添加书籍记录](media/tutorial-mean/meanstack-add.png)

4. 刷新页面后，应会看到以下页面所示的内容：

    ![列出书籍记录](media/tutorial-mean/meanstack-list.png)

5. 可以单击“删除”并从数据库中删除书籍记录。

## <a name="next-steps"></a>后续步骤

本教程已创建一个可在 Linux VM 上使用 MEAN 堆栈跟踪书籍记录的 Web 应用程序。 你已了解如何：

> [!div class="checklist"]
> * 创建 Linux VM
> * 安装 Node.js
> * 安装 MongoDB 并设置服务器
> * 安装 Express 并设置服务器的路由
> * 使用 AngularJS 访问路由
> * 运行应用程序

转到下一教程，了解如何使用 SSL 证书保护 Web 服务器。

> [!div class="nextstepaction"]
> [使用 SSL 保护 Web 服务器](tutorial-secure-web-server.md)
