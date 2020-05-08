---
title: 使用 Node.js 模块
description: 了解如何在使用 Azure 应用服务或云服务的同时使用 Node.js 模块。
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 8621dde43ff38cee15feb1d30b6ea80ab681c855
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594774"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>将 Node.js 模块与 Azure 应用程序一起使用
本文档提供有关将 Node.js 模块与托管在 Azure 中的应用程序一起使用的指南。 其中提供有关确保应用程序使用特定版本的模块以及对 Azure 使用本机模块的指南。

如果已了解如何使用 Node.js 模块、package.json 和 npm-shrinkwrap.json 文件，可参阅以下信息快速概览本文讨论的内容：  

* Azure 应用服务了解 **package.json** 和 **npm-shrinkwrap.json** 文件，可基于这些文件中的条目安装模块。

* Azure 云服务需要所有模块都安装在开发环境中，并将 node**modules 目录包含为部署包的一部分\_** 。 可为使用 package.json 或 npm-shrinkwrap.json 文件在云服务上安装模块提供相应支持，但此配置需要自定义云服务项目使用的默认脚本。   有关如何配置此环境的示例，请参阅[运行 npm install 避免部署 Node 模块的 Azure 启动任务](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> 本文不讨论 Azure 虚拟机，因为 VM 中的开发体验取决于虚拟机托管的操作系统。
> 
> 

## <a name="nodejs-modules"></a>Node.js 模块
模块是可加载的 JavaScript 包，可为应用程序提供特定功能。 通常使用 npm 命令行工具安装模块，但一些模块（如 http 模块）作为核心 Node.js 包的一部分提供。 

在安装模块后，这些模块存储在应用程序目录结构根目录下的 **node\_modules** 目录中。 node**modules 目录中的每个模块都保留自己的目录，其中包含它依赖的所有模块，此行为对于依赖项链上的每个模块均是如此\_** 。 此环境使得安装的每个模块均对其依赖的模块具有自己的版本要求，但这会导致目录结构很大。

与使用 package.json 或 npm-shrinkwrap.json 文件相比时，随附应用程序部署 node**modules 目录会增加部署的大小；但是，它确实可保证生产中使用的模块版本与开发中使用的相同。\_**  

### <a name="native-modules"></a>本机模块
虽然多数模块都只是纯文本 JavaScript 文件，但一些模块是特定于平台的二进制映像。 这些模块通常是在安装时使用 Python 和 node-gyp 编译的。 由于 Azure 云服务依赖作为应用程序一部分部署的 **node\_modules** 文件夹，因此只要作为已安装模块一部分包含的任何本机模块是在 Windows 开发系统中安装和编译的，那么该模块都应在云服务中运行。

Azure 应用服务不支持部分本机模块，并且在编译具有特定先决条件的模块时可能失败。 虽然一些常用模块（如 MongoDB）可选择性使用本机依赖项，且没有这些依赖项也可正常工作，但经证明有两种解决方法几乎可成功适用于当今所有本机模块：

* 在安装了所有系统必备组件的 Windows 计算机运行 **npm install**。 然后，将创建的 **node\_modules** 文件夹作为应用程序的一部分部署到 Azure 应用服务。

  * 在编译之前, 检查本地 Node.js 安装是否具有匹配的体系结构以及版本是否尽可能接近在 Azure 中使用的版本（可以在运行时从属性 **process.arch** 和 **process.version** 中检查当前值）。

* 可以将 Azure 应用服务配置为在部署期间执行自定义 bash 或 shell 脚本，以便能够执行自定义命令并精确配置 **npm install** 的运行方式。 有关演示如何配置该环境的视频，请参阅[使用 Kudu 自定义网站部署脚本](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/)。

### <a name="using-a-packagejson-file"></a>使用 package.json 文件

可使用 **package.json** 文件来指定应用程序所需的顶级依赖项，以便托管平台能够安装这些依赖项，而不是要求在部署中包含 **node\_modules** 文件夹。 部署应用程序后，可使用 **npm install** 命令分析 **package.json** 文件并安装列出的所有依赖项。

在开发期间，可以在安装模块时使用 **--save**、 **--save-dev** 或 **--save-optional** 参数，自动将模块条目添加到 **package.json** 文件中。 有关详细信息，请参阅 [npm-install](https://docs.npmjs.com/cli/install)。

package.json 文件的一个潜在问题是它仅指定顶级依赖项的版本。  安装的每个模块不一定会指定它所依赖的模块的版本，因此最终使用的依赖项链可能与开发过程中使用的不同。

> [!NOTE]
> 部署到 Azure 应用服务时，如果 package.json 文件引用本机模块，在使用 Git 发布应用程序时可能会看到与以下示例类似的错误：<b></b>
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>使用 npm-shrinkwrap.json 文件
**npm-shrinkwrap.json** 文件用于尝试消除 **package.json** 文件的模块版本控制限制。 虽然 package.json 文件仅包含顶级模块的版本，但 npm-shrinkwrap.json 文件包含所有模块依赖项链的版本要求。  

应用程序准备好生产后，便可锁定版本要求，使用 npm shrinkwrap 命令创建 npm-shrinkwrap.json 文件。   此命令会使用当前安装在 node**modules 文件夹中的版本，并将这些版本记录到 npm-shrinkwrap.json 文件。\_**  将应用程序部署到托管环境后，可使用 **npm install** 命令来分析 **npm-shrinkwrap.json** 文件并安装列出的所有依赖项。 有关详细信息，请参阅 [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)。

> [!NOTE]
> 部署到 Azure 应用服务时，如果 npm-shrinkwrap.json 文件引用本机模块，在使用 Git 发布应用程序时可能会看到与以下示例类似的错误：<b></b>
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

## <a name="next-steps"></a>后续步骤
了解如何将 Node.js 模块与 Azure 一起使用后，请学习如何[指定 Node.js 版本](https://github.com/squillace/nodejs-microservice)、[生成和部署 Node.js Web 应用](app-service/app-service-web-get-started-nodejs.md)，以及[如何使用适用于 Mac 和 Linux 的 Azure 命令行接口](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)。

有关详细信息，请参阅 [Node.js 开发人员中心](/azure/javascript/)。

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
