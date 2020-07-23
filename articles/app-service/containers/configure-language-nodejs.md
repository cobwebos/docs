---
title: 配置 Node.js 应用
description: 了解如何为应用配置预建 Node.js 容器。 本文介绍最常见的配置任务。
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 699c77e937fc13cadf742d193ab1b0b8f00a2726
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905708"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>为 Azure App Service 配置 Linux Node.js 应用

必须部署所有必需的 NPM 依赖项，才能部署 Node.js 应用。 `npm install --production`部署[Git 存储库](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)时，或在启用生成过程的情况下，应用服务部署引擎（Kudu [Zip package](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ）会自动运行。 但是，如果使用[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)部署文件，则需要手动上传所需的包。

本指南为在应用服务中使用内置 Linux 容器的 Node.js 开发人员提供重要的概念和说明。 如果你从未使用过 Azure App Service，请先参阅[Node.js 快速入门](quickstart-nodejs.md)教程，并[Node.js MongoDB 教程](tutorial-nodejs-mongodb-app.md)。

## <a name="show-nodejs-version"></a>显示 Node.js 版本

若要显示当前 Node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有支持的 Node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>设置 Node.js 版本

若要将应用设置为[受支持的 Node.js 版本](#show-nodejs-version)，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此设置指定在运行时和 Kudu 中自动包还原期间使用的 Node.js 版本。

> [!NOTE]
> 应在项目的中设置 Node.js 版本 `package.json` 。 部署引擎在包含所有受支持的 Node.js 版本的单独容器中运行。

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务生成自动化将按以下顺序完成各个步骤：

1. 运行 `PRE_BUILD_SCRIPT_PATH` 指定的自定义脚本。
1. `npm install`无需任何标志即可运行，其中包括 npm `preinstall` 和 `postinstall` 脚本，也会进行安装 `devDependencies` 。
1. `npm run build`如果在*的package.js*中指定了生成脚本，则运行。
1. `npm run build:azure`如果生成： azure 脚本是在*上的package.js*中指定的，则运行。
1. 运行 `POST_BUILD_SCRIPT_PATH` 指定的自定义脚本。

> [!NOTE]
> 如[npm 文档](https://docs.npmjs.com/misc/scripts)中所述，在 `prebuild` `postbuild` 指定的之前和之后分别运行的脚本 `build` 。 `preinstall`并 `postinstall` 分别在之前和之后运行 `install` 。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认为空的环境变量。 若要运行生成前命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

以下示例在一系列命令中指定两个以逗号分隔的变量。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关用于自定义生成自动化的其他环境变量，请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要详细了解应用服务的运行方式以及如何在 Linux 中构建 Node.js 应用，请参阅[Oryx 文档：如何检测和生成 Node.js 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>配置 Node.js 服务器

Node.js 容器附带了一个生产流程经理[PM2](https://pm2.keymetrics.io/)。 你可以将应用程序配置为以 PM2 或使用 NPM 或使用自定义命令开始。

- [运行自定义命令](#run-custom-command)
- [运行 npm 开始](#run-npm-start)
- [用 PM2 运行](#run-with-pm2)

### <a name="run-custom-command"></a>运行自定义命令

应用服务可以使用自定义命令（如*run.sh*等可执行文件）启动应用。例如，若要运行 `npm run start:prod` ，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>运行 npm 开始

若要使用启动应用程序 `npm start` ，只需确保 `start` *package.js*文件中有脚本。 例如：

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

若要在项目中使用自定义*package.js* ，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>用 PM2 运行

当在你的项目中找到一个公用 Node.js 文件时，容器会自动通过 PM2 启动你的应用程序：

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 以下[PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)之一： *process.js*和*ecosystem.config.js*

你还可以使用以下扩展配置自定义启动文件：

- *.Js*文件
- 扩展名为*json*、 *.config.js*、 *. Yaml*或 *. docker-compose.override.yml*的[PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

若要添加自定义起始文件，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>远程调试

> [!NOTE]
> 远程调试当前为预览版。

如果将应用程序配置为使用[PM2 运行](#run-with-pm2)，则可以在[Visual Studio Code](https://code.visualstudio.com/)中远程调试 Node.js 应用程序，除非使用 * .config.js、docker-compose.override.yml 或*yaml*运行该应用程序。

在大多数情况下，你的应用程序不需要进行额外配置。 如果你的应用程序是使用*process.js*文件（默认或自定义）运行的，则该应用程序必须具有 `script` JSON 根中的属性。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要设置远程调试的 Visual Studio Code，请安装[应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 按照 "扩展" 页上的说明操作，并在 Visual Studio Code 中登录到 Azure。

在 Azure 资源管理器中，找到要调试的应用程序，右键单击该应用，然后选择 "**开始远程调试**"。 单击 **"是"** 以启用应用。 应用服务启动隧道代理并附加调试器。 然后，您可以向应用程序发出请求，并查看调试器在断点处暂停。

完成调试后，通过选择 "**断开连接**" 来停止调试器。 出现提示时，应单击 **"是"** 以禁用远程调试。 若要以后再禁用它，请在 Azure 资源管理器中再次右键单击你的应用程序，然后选择 "**禁用远程调试**"。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，你可以使用标准 Node.js 模式访问它们。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

默认情况下，Kudu `npm install --production` 会在识别到部署 Node.js 应用时运行。 如果应用需要任何常用的自动化工具，如 Grunt、Bower 或 Gulp，则需要提供一个[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)来运行该工具。

若要使存储库能够运行这些工具，需要将它们添加到*package.js上*的依赖项。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

在本地终端窗口中，将目录更改为存储库根，并运行以下命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存储库根现在包含两个附加文件： *. deployment*和*deploy.sh*。

打开*deploy.sh*并找到如下所 `Deployment` 示的部分：

```bash
##################################################################################################################################
# Deployment
# ----------
```

此部分结束于运行 `npm install --production` 。 添加代码部分，需要*在节末尾*运行所需的工具 `Deployment` ：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅[MEAN.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，在该示例中，部署脚本也运行自定义 `npm install` 命令。

### <a name="bower"></a>Bower

此代码段将运行 `bower install` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码段将运行 `gulp imagemin` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码段将运行 `grunt` 。

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

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在[Express](https://expressjs.com/)中，可以使用[信任代理](https://expressjs.com/guide/behind-proxies.html)。 例如：

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

如果工作 Node.js 应用在应用服务中的行为不同，或者有错误，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行 Node.js 应用，因此需要确保项目在生产模式下按预期在本地运行。 例如：
    - 根据的*package.js*，可能会为生产模式（与）安装不同的包 `dependencies` `devDependencies` 。
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在开发模式下，在应用服务中运行应用。 例如，在[MEAN.js](https://meanjs.org/)中，可以通过[设置 `NODE_ENV` 应用程序设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，将应用程序设置为运行时中的开发模式。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 MongoDB 的 Node.js 应用](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
