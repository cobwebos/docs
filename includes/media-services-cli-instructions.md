---
title: include 文件
description: include 文件
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155717"
---
## <a name="cli-shell"></a>CLI Shell

建议使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 来执行 CLI 命令。 **Cloud Shell** 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 它使用户能够灵活选择最适合自己工作方式的 shell 体验。 Linux 用户可以选择 Bash 体验，Windows 用户可以选择 PowerShell。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 

### <a name="login"></a>登录

若要开始使用 CLI shell（在云中或本地），请运行 `az login` 来创建与 Azure 的连接。

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开一个浏览器页面，在浏览器中导航到 https://aka.ms/devicelogin 后，按照有关命令行的说明输入授权代码。

### <a name="specify-location-of-files"></a>指定文件位置

许多媒体服务 CLI 命令允许你通过文件名来传递参数。 如果使用 Cloud Shell，可以将文件上传到 clouddrive（方法是使用 Bash 或 PowerShell）。 

![上传文件]

无论使用本地 CLI 还是 Cloud Shell，都需要根据所用的 OS 或 Cloud Shell（Bash 或 PowerShell）指定文件路径。 下面是一些示例：

文件（所有 OS）的相对路径

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac 和 Windows OS 上的绝对文件路径

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

如果命令要求提供文件路径，请使用 `{file}`。 例如，`az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`。 <br/> 如果命令将加载指定的文件，请使用 `@{file}`。 例如，`az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`。

[上传文件]: ./media/media-services-cli/upload-download-files.png
