---
title: include 文件
description: include 文件
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104977"
---
## <a name="open-cli-shell"></a>打开 CLI Shell

建议使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 来执行 CLI 命令。 **Cloud Shell** 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 只需要选择“复制”按钮来复制代码，将其粘贴到 Cloud Shell 中，然后按 Enter 键来运行它。 可通过多种方式打开 Cloud Shell：

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 

### <a name="login"></a>登录

若要开始使用 CLI shell（在云中或本地），请运行 `az login` 来创建与 Azure 的连接。

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开一个浏览器页面，在浏览器中导航到 https://aka.ms/devicelogin 后，按照有关命令行的说明输入授权代码。

### <a name="specify-location-of-files"></a>指定文件位置

许多媒体服务 CLI 命令允许你通过文件名来传递参数。 

如果使用的是 **Azure Cloud Shell**，请将文件上传到 **Azure Cloud Shell**。 可以在 shell 窗口的顶部找到上传/下载文件按钮。 然后，如下所示来引用文件：`@{FileName}.` 

![上传文件]

如果在本地使用 Azure CLI，请指定整个文件路径。 例如，`@c:\tracks.json`。


[上传文件]: ./media/media-services-cli/upload-download-files.png
