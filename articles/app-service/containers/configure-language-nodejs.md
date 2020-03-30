---
title: 配置 Node.js 应用
description: 了解如何为应用配置预构建的 Node.js 容器。 本文介绍最常见的配置任务。
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252722"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux Node.js 应用

必须使用所有必需的 NPM 依赖项部署 Node.js 应用。 当您部署[Git 存储库](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或启动生成进程的`npm install --production`Zip[包](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)时，应用服务部署引擎 （Kudu） 会自动为您运行。 但是，如果使用[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)部署文件，则需要手动上载所需的包。

本指南为在应用服务中使用内置 Linux 容器的 Node.js 开发人员提供了关键概念和说明。 如果您从未使用过 Azure 应用服务，请先使用 MongoDB 教程，请先按照[Node.js 快速入门](quickstart-nodejs.md)和[Node.js 进行教程](tutorial-nodejs-mongodb-app.md)。

## <a name="show-nodejs-version"></a>显示 Node.js 版本

要显示当前 Node.js 版本，在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 Node.js 版本，可在[云外壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>设置 Node.js 版本

要将应用设置为[受支持的 Node.js 版本](#show-nodejs-version)，请在[云外壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此设置指定在运行时和 Kudu 自动包还原期间要使用的 Node.js 版本。

> [!NOTE]
> 应在项目的 中设置 Node.js 版本`package.json`。 部署引擎在包含所有受支持的 Node.js 版本的单独容器中运行。

## <a name="customize-build-automation"></a>自定义构建自动化

如果使用打开的 Git 或 zip 包部署应用，应用服务将构建自动化步骤，执行以下顺序：

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。
1. 运行`npm install`没有任何标志，其中包括 npm`preinstall`和`postinstall`脚本，并且还安装`devDependencies`。
1. 如果在`npm run build`*包*中指定了生成脚本，请运行 。
1. 如果在`npm run build:azure`*包*中指定了生成：azure 脚本，请运行 。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。

> [!NOTE]
> 如[npm 文档中](https://docs.npmjs.com/misc/scripts)所述，如果指定`prebuild`，`postbuild`分别在`build`之前和之后命名和运行的脚本。 `preinstall`并`postinstall`分别在之前和之后`install`运行。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是默认情况下为空的环境变量。 要运行预生成命令，请定义`PRE_BUILD_COMMAND`。 要运行生成后命令，请定义`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的两个变量，这些命令用逗号分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关自定义生成自动化的其他环境变量，请参阅[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何运行和构建 Linux 中的 Node.js 应用的详细信息，请参阅[Oryx 文档：如何检测和生成 Node.js 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>配置 Node.js 服务器

Node.js 容器附带生产流程管理器[PM2。](https://pm2.keymetrics.io/) 您可以将应用配置为从 PM2、NPM 或自定义命令开始。

- [运行自定义命令](#run-custom-command)
- [运行 npm 开始](#run-npm-start)
- [使用 PM2 运行](#run-with-pm2)

### <a name="run-custom-command"></a>运行自定义命令

应用服务可以使用自定义命令启动应用，例如*run.sh*等可执行文件。例如，要运行`npm run start:prod`，在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>运行 npm 开始

要使用`npm start`启动应用，只需确保`start`脚本位于*包.json*文件中。 例如：

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

要在项目中使用自定义*包.json，* 请在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>使用 PM2 运行

当项目中找到一个常见的 Node.js 文件时，容器会自动使用 PM2 启动应用：

- *宾/www*
- *server.js*
- *app.js*
- *index.js*
- *托管 start.js*
- 以下[PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)之一 ：*进程.json*和*生态系统.config.js*

您还可以配置具有以下扩展名的自定义启动文件：

- *.js*文件
- 具有扩展名 .json、.config.js、.yaml *.yaml*或 *.yml*的 *.json*[PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)*.config.js*

要添加自定义启动文件，在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>远程调试

> [!NOTE]
> 远程调试当前处于预览状态。

如果将 Node.js 应用配置为[使用 PM2 运行](#run-with-pm2)，则可以在[Visual Studio 代码](https://code.visualstudio.com/)中远程调试它，但使用 *.config.js、*.yml 或 *.yaml*运行该应用时除外。

在大多数情况下，你的应用不需要额外的配置。 如果应用使用*进程.json*文件（默认或自定义）运行，则它必须在 JSON 根中具有属性`script`。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

要设置用于远程调试的可视化工作室代码，请安装[应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 按照扩展页上的说明操作，并在可视化工作室代码中登录到 Azure。

在 Azure 资源管理器中，查找要调试的应用，右键单击它并选择 **"启动远程调试**"。 单击"**是**"可为应用启用它。 应用服务为您启动隧道代理并附加调试器。 然后，您可以向应用发出请求，并看到调试器在断点暂停。

完成调试后，通过选择 **"断开连接"** 停止调试器。 当出现提示时，应单击 **"是**"以禁用远程调试。 要稍后禁用它，请在 Azure 资源管理器中再次右键单击应用，然后选择 **"禁用远程调试**"。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，您可以使用标准 Node.js 模式访问它们。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行格伦特/鲍尔/古尔普

默认情况下，库杜在部署`npm install --production`Node.js 应用时运行。 如果你的应用需要任何流行的自动化工具，如 Grunt、Bower 或 Gulp，则需要提供[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)来运行它。

要使存储库能够运行这些工具，您需要将它们添加到*包的依赖项。* 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

从本地终端窗口，将目录更改为存储库根目录并运行以下命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存储库根现在有两个附加文件 *：.部署*和*deploy.sh*。

打开*deploy.sh*并查找`Deployment`该部分，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本节以运行`npm install --production`结尾。 添加在`Deployment`本节*末尾*运行所需工具所需的代码部分：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅[MEAN.js 示例中的一个示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中部署脚本还运行自定义`npm install`命令。

### <a name="bower"></a>Bower

此代码段`bower install`运行 。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码段`gulp imagemin`运行 。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码段`grunt`运行 。

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

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在[Express](https://expressjs.com/)中，您可以使用[信任代理](https://expressjs.com/guide/behind-proxies.html)。 例如：

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

## <a name="troubleshooting"></a>疑难解答

当工作 Node.js 应用在应用服务中的行为不同或有错误时，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下本地测试应用。 应用服务在生产模式下运行 Node.js 应用，因此您需要确保项目在本地生产模式下按预期工作。 例如：
    - 根据您的*包.json，* 不同的软件包可能安装不同的生产模式（`dependencies`与 。 `devDependencies`
    - 某些 Web 框架在生产模式下可能以不同的方式部署静态文件。
    - 某些 Web 框架在生产模式下运行时可能会使用自定义启动脚本。
- 在开发模式下在应用服务中运行应用。 例如，在[MEAN.js](https://meanjs.org/)中，您可以通过[设置`NODE_ENV`应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)在运行时将应用设置为开发模式。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程： Node.js 应用程序与蒙戈DB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
