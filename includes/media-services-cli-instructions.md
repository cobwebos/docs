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
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79461208"
---
## <a name="use-cli-shell"></a>使用 CLI Shell

建议使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 来执行 CLI 命令。 **Cloud Shell** 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 它使用户能够灵活选择最适合自己工作方式的 shell 体验。 Linux 用户可以选择 Bash 体验，Windows 用户可以选择 PowerShell。

也可以在本地安装 CLI。 有关适用于你的平台的说明，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="sign-in"></a>登录

使用本地安装的 CLI 需要登录到 Azure。 对于 Azure Cloud Shell 不需要执行此步骤。 使用 `az login` 命令登录。

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开一个浏览器页面，在浏览器中导航到 https://aka.ms/devicelogin 后，按照有关命令行的说明输入授权代码。

### <a name="specify-location-of-files"></a>指定文件位置

许多媒体服务 CLI 命令允许你通过文件名来传递参数。 如果使用 Cloud Shell，可以将文件上传到 clouddrive（方法是使用 Bash 或 PowerShell）  。 

![上传文件]

无论使用本地 CLI 还是 Cloud Shell，都需要根据所用的 OS 或 Cloud Shell（Bash 或 PowerShell）指定文件路径  。 下面是一些示例：

文件（所有 OS）的相对路径

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac 和 Windows OS 上的绝对文件路径

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

如果命令要求提供文件路径，请使用 `{file}`。 例如，`az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json` 。 <br/> 如果命令将加载指定的文件，请使用 `@{file}`。 例如，`az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json` 。

[上传文件]: ./media/media-services-cli/upload-download-files.png
