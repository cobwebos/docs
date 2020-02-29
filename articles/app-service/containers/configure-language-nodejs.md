---
title: 配置 node.js 应用
description: 了解如何为应用程序配置预建 node.js 容器。 本文介绍最常见的配置任务。
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 45d7d141bc2ab85ab33be455fc3da5570b0e7f51
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920019"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>为 Azure App Service 配置 Linux node.js 应用

Node.js 应用必须部署所有必需的 NPM 依赖项。 部署[Git 存储库](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)时，应用服务部署引擎（Kudu）会自动运行 `npm install --production`，或在启用生成过程的情况下运行[Zip 包](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。 但是，如果使用[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)部署文件，则需要手动上传所需的包。

本指南提供了在应用服务中使用内置 Linux 容器的 node.js 开发人员的重要概念和说明。 如果你从未使用过 Azure App Service，请先遵循 node.js[快速入门](quickstart-nodejs.md)和 Node.js [with MongoDB 教程](tutorial-nodejs-mongodb-app.md)。

## <a name="show-nodejs-version"></a>显示 node.js 版本

若要显示当前 Node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有支持的 node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>设置 node.js 版本

若要将应用设置为[支持的 node.js 版本](#show-nodejs-version)，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此设置指定在运行时和 Kudu 中自动包还原期间要使用的 node.js 版本。

> [!NOTE]
> 应在项目的 `package.json`中设置 node.js 版本。 部署引擎在单独的容器中运行，其中包含所有受支持的 node.js 版本。

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用了生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务将通过以下顺序生成自动化步骤：

1. 如果 `PRE_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。
1. 无需任何标志即可运行 `npm install`，其中包括 npm `preinstall` 和 `postinstall` 脚本，还会安装 `devDependencies`。
1. 如果在*包*中指定了生成脚本，则运行 `npm run build`。
1. 如果生成，请运行 `npm run build:azure`： azure 脚本是在*包 json*中指定的。
1. 如果 `POST_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。

> [!NOTE]
> 如[npm 文档](https://docs.npmjs.com/misc/scripts)中所述，名为 `prebuild` 和 `postbuild` 的脚本分别在 `build`之前和之后运行（如果已指定）。 `preinstall` 和 `postinstall` 分别在 `install`之前和之后运行。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认情况下为空的环境变量。 若要运行预生成命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

下面的示例为一系列命令指定了两个变量，用逗号分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关自定义生成自动化的其他环境变量，请参阅[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要详细了解应用服务的运行方式以及如何在 Linux 中生成 node.js 应用，请参阅[Oryx 文档：如何检测和生成 node.js 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>配置 node.js 服务器

Node.js 容器附带了[PM2](https://pm2.keymetrics.io/)，即生产流程管理器。 你可以将应用程序配置为以 PM2 或使用 NPM 或使用自定义命令开始。

- [运行自定义命令](#run-custom-command)
- [运行 npm 开始](#run-npm-start)
- [用 PM2 运行](#run-with-pm2)

### <a name="run-custom-command"></a>运行自定义命令

应用服务可以使用自定义命令（如*run.sh*等可执行文件）启动应用。例如，若要运行 `npm run start:prod`，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>运行 npm 开始

若要使用 `npm start`启动应用程序，只需确保 `start` 脚本位于*package*文件中。 例如：

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

若要在项目中使用自定义*package* ，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>用 PM2 运行

当在你的项目中找到一个公用 node.js 文件时，容器会自动通过 PM2 启动你的应用程序：

- *bin/www*
- *node.js*
- *app.js*
- *index.js*
- *hostingstart.html*
- 以下[PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)之一：*进程 json*和*生态系统*

你还可以使用以下扩展配置自定义启动文件：

- *.Js*文件
- 扩展名为*json*、PM2 *、* *. Yaml*或 *. docker-compose.override.yml*的[文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

若要添加自定义起始文件，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>远程调试

> [!NOTE]
> 远程调试当前为预览版。

如果将 PM2 配置为使用[运行](#run-with-pm2)，则可以在[Visual Studio Code](https://code.visualstudio.com/)中远程调试 node.js 应用程序，除非使用 * .config、docker-compose.override.yml 或*yaml*运行它。

在大多数情况下，你的应用程序不需要进行额外配置。 如果你的应用程序是使用*进程 json*文件（默认或自定义）运行的，则它必须在 json 根中具有 `script` 属性。 例如：

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

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，你可以使用标准 node.js 模式访问它们。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

默认情况下，Kudu 在识别 node.js 应用时 `npm install --production` 运行。 如果应用需要任何常用的自动化工具，如 Grunt、Bower 或 Gulp，则需要提供一个[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)来运行该工具。

若要使存储库能够运行这些工具，需要将它们添加到*包 json*中的依赖项。 例如：

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

打开*deploy.sh*并查找 `Deployment` 节，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本部分结束于运行 `npm install --production`。 添加代码部分，需要在 `Deployment` 部分的*末尾*运行所需的工具：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅[node.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中，部署脚本也运行自定义 `npm install` 命令。

### <a name="bower"></a>Bower

此代码片段运行 `bower install`。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码片段运行 `gulp imagemin`。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码片段运行 `grunt`。

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

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>故障排除

如果工作 Node.js 应用在应用服务中的行为方式不同或出现错误，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下本地测试应用。 应用服务在生产模式下运行 node.js 应用，因此，你需要确保你的项目在生产模式下以本地方式按预期方式工作。 例如：
    - 根据*包*的不同，可能会为生产模式安装不同的包（`dependencies` 与 `devDependencies`）。
    - 某些 web 框架可以在生产模式下以不同的方式部署静态文件。
    - 在生产模式下运行时，某些 web 框架可能使用自定义的启动脚本。
- 在开发模式下，在应用服务中运行应用。 例如[，在中](https://meanjs.org/)，可以通过[设置 "`NODE_ENV` 应用" 设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，将应用程序设置为运行时中的开发模式。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：具有 MongoDB 的 node.js 应用](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
