---
title: 配置 Windows Node.js 应用
description: 了解如何在应用服务的本机 Windows 实例中配置 Node.js 应用。 本文介绍最常见的配置任务。
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907870"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>为 Azure App Service 配置 Windows Node.js 应用

必须部署所有必需的 NPM 依赖项，才能部署 Node.js 应用。 `npm install --production`部署[Git 存储库](deploy-local-git.md)或启用了生成自动化的[Zip 包](deploy-zip.md)时，应用服务部署引擎会自动运行。 但是，如果使用[FTP/S](deploy-ftp.md)部署文件，则需要手动上传所需的包。 有关 Linux 应用的信息，请参阅为[Azure App Service 配置 LINUX PHP 应用](containers/configure-language-nodejs.md)。

本指南为部署到应用服务的 Node.js 开发人员提供重要的概念和说明。 如果你从未使用过 Azure App Service，请先参阅[Node.js 快速入门](app-service-web-get-started-nodejs.md)教程，并[Node.js MongoDB 教程](app-service-web-tutorial-nodejs-mongodb-app.md)。

## <a name="show-nodejs-version"></a>显示 Node.js 版本

若要显示当前 Node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

若要显示所有支持的 Node.js 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>设置 Node.js 版本

若要将应用设置为[受支持的 Node.js 版本](#show-nodejs-version)，请在[Cloud Shell](https://shell.azure.com)中运行以下命令，以将设置 `WEBSITE_NODE_DEFAULT_VERSION` 为受支持的版本：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

此设置指定在运行时和应用服务生成自动化期间自动包还原期间使用的 Node.js 版本。

> [!NOTE]
> 应在项目的中设置 Node.js 版本 `package.json` 。 部署引擎在单独的进程中运行，该进程包含所有受支持的 Node.js 版本。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md)。 然后，你可以使用标准 Node.js 模式访问它们。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

默认情况下，应用服务生成自动化 `npm install --production` 在识别通过 Git （或启用了生成自动化的 Zip 部署）部署的 Node.js 应用时运行。 如果应用需要任何常用的自动化工具，如 Grunt、Bower 或 Gulp，则需要提供一个[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)来运行该工具。

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

如果工作 Node.js 应用在应用服务中的行为不同，或者有错误，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行 Node.js 应用，因此需要确保项目在生产模式下按预期在本地运行。 例如：
    - 根据的*package.js*，可能会为生产模式（与）安装不同的包 `dependencies` `devDependencies` 。
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在开发模式下，在应用服务中运行应用。 例如，在[MEAN.js](https://meanjs.org/)中，可以通过[设置 `NODE_ENV` 应用程序设置](configure-common.md)，将应用程序设置为运行时中的开发模式。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 MongoDB 的 Node.js 应用](app-service-web-tutorial-nodejs-mongodb-app.md)

