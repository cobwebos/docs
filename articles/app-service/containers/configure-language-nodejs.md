---
title: 配置 Node.js 应用程序-Azure 应用服务 |Microsoft Docs
description: 了解如何配置要在 Azure 应用服务中运行的 Node.js 应用程序
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551317"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>为 Azure 应用服务中配置 Linux Node.js 应用

必须具有所有必需 NPM 依赖项部署 Node.js 应用程序。 应用服务部署引擎 (Kudu) 自动运行`npm install --production`为你在部署时[Git 存储库](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，或[Zip 包](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)与打开的生成过程。 如果你使用的文件部署[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，但是，您需要手动上传所需的包。

本指南提供的重要概念和面向 Node.js 开发人员在应用服务中使用内置的 Linux 容器的说明。 如果你从未使用过 Azure 应用服务，请按照[Node.js 快速入门](quickstart-nodejs.md)并[Node.js 与 MongoDB 教程](tutorial-nodejs-mongodb-app.md)第一个。

## <a name="show-nodejs-version"></a>显示 Node.js 版本

若要显示当前的 Node.js 版本，请运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有受支持的 Node.js 版本，请运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>设置 Node.js 版本

若要将您的应用程序设置为[受支持的 Node.js 版本](#show-nodejs-version)，在中运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此设置指定要使用，在运行时和在 Kudu 中自动的包还原期间的 Node.js 版本。

> [!NOTE]
> 应在项目中设置的 Node.js 版本`package.json`。 在包含所有受支持的 Node.js 版本的单独容器中运行部署引擎。

## <a name="configure-nodejs-server"></a>配置 Node.js 服务器

Node.js 容器附带[PM2](http://pm2.keymetrics.io/)，生产流程管理器。 你可以配置应用以启动 PM2，或 NPM，或自定义命令。

- [运行自定义命令](#run-custom-command)
- [运行 npm start](#run-npm-start)
- [使用 PM2 运行](#run-with-pm2)

### <a name="run-custom-command"></a>运行自定义命令

应用服务可以启动应用程序使用自定义命令，如可执行文件像一样*run.sh*。例如，若要运行`npm run start:prod`，在中运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>运行 npm start

若要开始在应用程序使用`npm start`，只需确保`start`脚本位于*package.json*文件。 例如：

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

若要使用自定义*package.json*在项目中，在中运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>使用 PM2 运行

容器在你的项目中找到的一个常见的 Node.js 文件时，自动将您的应用程序启动使用 PM2:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 以下值之一[PM2 文件](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json*和*ecosystem.config.js*

此外可以使用以下扩展配置的自定义启动文件：

- 一个 *.js*文件
- 一个[PM2 文件](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)扩展名 *.json*， *。 config.js*， *.yaml*，或 *.yml*

若要添加自定义启动文件，请运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>远程调试

> [!NOTE]
> 远程调试当前处于预览状态。

可以调试 Node.js 应用程序中远程[Visual Studio Code](https://code.visualstudio.com/)如果你配置[使用 PM2 运行](#run-with-pm2)，它使用运行时除外 *。 config.js、 *.yml，或 *.yaml*。

在大多数情况下，没有额外的配置都需要为你的应用。 如果您的应用程序运行了*process.json*文件 （默认或自定义），它必须具有`script`JSON 根目录中的属性。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要进行远程调试设置 Visual Studio Code，安装[应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 按照扩展页上的说明和登录到 Visual Studio Code 中的 Azure。

在 Azure 资源管理器，找到你想要调试，右键单击它并选择的应用**启动远程调试**。 单击**是**以便为您的应用程序启用它。 应用服务为你的隧道代理将启动，并附加调试器。 然后，可以向应用程序发出请求，并查看调试器断点处暂停。

一旦完成调试，通过选择停止调试器**断开连接**。 出现提示时，您应该单击**是**要禁用远程调试。 若要禁用更高版本，请右键单击在 Azure 资源管理器中再次应用并选择**禁用远程调试**。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，你可以[设置应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)外部应用程序代码。 然后您可以访问它们使用标准 Node.js 模式。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

默认情况下，Kudu 运行`npm install --production`时它可以识别部署 Node.js 应用。 如果你的应用需要的任何常用自动化工具，如 Grunt、 Bower 或 Gulp，则需要提供[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)来运行它。

若要启用你的存储库来运行这些工具，您需要将它们添加到中的依赖项*package.json。* 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

从本地终端窗口中，将目录更改为存储库根路径并运行以下命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存储库根路径现在有两个其他文件： *.deployment*并*deploy.sh*。

打开*deploy.sh*并找到`Deployment`部分中，类似如下：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本部分结尾运行`npm install --production`。 添加的代码段，您需要运行所需的工具*末尾*的`Deployment`部分：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅[MEAN.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，部署脚本还运行自定义`npm install`命令。

### <a name="bower"></a>Bower

此代码片段运行`bower install`。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码片段运行`gulp imagemin`。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码片段运行`grunt`。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要检查用户请求是否已加密，可以检查 `X-Forwarded-Proto` 标头。

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在中[Express](https://expressjs.com/)，可以使用[信任代理](http://expressjs.com/guide/behind-proxies.html)。 例如：

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>故障排除

当工作的 Node.js 应用程序行为是不同的应用服务中，或有错误时，请尝试以下方法：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，本地测试应用程序。 应用服务在生产模式下运行 Node.js 应用程序，因此您需要确保你的项目按预期方式在本地生产模式下能够正常工作。 例如：
    - 具体取决于你*package.json*，可能会为生产模式下安装不同的包 (`dependencies`与`devDependencies`)。
    - 某些 web 框架可以部署在生产模式下以不同的方式的静态文件。
    - 在生产模式下运行时，某些 web 框架可能会使用自定义启动脚本。
- 在开发模式下，在应用服务中运行你的应用。 例如，在[MEAN.js](http://meanjs.org/)，可以将您的应用程序设置为在运行时中的开发模式[设置`NODE_ENV`应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 MongoDB 的 Node.js 应用](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Linux 版应用服务常见问题解答](app-service-linux-faq.md)