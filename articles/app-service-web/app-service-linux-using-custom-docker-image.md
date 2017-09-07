---
title: "如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像 | Microsoft Docs"
description: "如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像。"
keywords: "azure 应用服务、web 应用、linux、docker、容器"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1458217a31c4781b28877c030a665f5b22819e13
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>使用 Linux 上 Azure Web 应用的自定义 Docker 映像 #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


在 Linux 上，应用服务提供预定义的应用程序堆栈，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 Linux 上应用服务使用 Docker 容器来托管这些预生成的应用程序堆栈。 还可使用自定义 Docker 映像将 Web 应用部署到尚未在 Azure 中定义的应用程序堆栈。 可以在公共或私有 Docker 存储库上托管自定义 Docker 映像。


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>如何：设置 Web 应用的自定义 Docker 映像
可以为新的 Web 应用和现有 Web 应用设置自定义 Docker 映像。 在 [Azure 门户](https://portal.azure.com/#create/Microsoft.AppSvcLinux)中创建 Linux Web 应用时，单击“配置容器”以设置自定义 Docker 映像：

![Linux 上新 Web 应用的自定义 Docker 映像][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>如何：使用 Docker 中心内的自定义 Docker 映像 ##
使用 Docker 中心内的自定义 Docker 映像：

1. 在 [Azure 门户](https://portal.azure.com)中找到 Linux Web 应用，并在“设置”中单击“Docker 容器”。

2.  选择“Docker 中心”作为“映像源”，并单击“公共”或“私有”并键入“映像和可选标记名称”，如 `node:4.5`。 将根据 Docker 映像文件中定义的内容自动设置“启动命令”，但也可以设置自己的命令。  

    ![配置 Docker 中心公共存储库映像][2]

    如果映像来自私有存储库，则还需输入私有 Docker 中心存储库的 Docker 中心凭据（即“登录用户名”和“密码”）。

    ![配置 Docker 中心私有存储库映像][3]

3. 配置容器后，单击“保存”。

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>如何使用私有映像注册表中的 Docker 映像 ##
使用私有映像注册表中的自定义 Docker 映像

1. 在 [Azure 门户](https://portal.azure.com)中找到 Linux Web 应用，并在“设置”中单击“Docker 容器”。

2.  单击“专用注册表”作为**映像源**。 输入专用注册表的**图像和可选标记名**、**服务器 URL**以及凭据（**登录用户名**和**密码**）。 单击“保存” 。

    ![配置私有注册表中的 Docker 映像][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>如何：设置 Docker 映像所使用的端口 ##

使用 Web 应用的自定义 Docker 映像时，可使用 Dockerfile 中的 `WEBSITES_PORT` 变量，此变量会被添加到生成的容器中。 请考虑下面的 Ruby 应用程序的 Docker 文件示例：

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p WEBSITES_PORT -e production

在命令的最后一行上，可以看到环境变量 WEBSITES_PORT 在运行时进行传递。 请记住，命令是区分大小写的。

平台之前使用的是 `PORT` 应用设置，我们计划弃用此应用设置，改为以独占方式使用 `WEBSITES_PORT`。

如果使用的现有 Docker 映像是由其他人生成的，则可能需要为应用程序指定一个不是端口 80 的端口。 若要配置端口，请添加名为 `WEBSITES_PORT` 的应用程序设置，其值如下所示：

![配置自定义 Docker 映像的端口应用设置][6]

## <a name="how-to-set-the-startup-time-for-your-docker-image"></a>如何：设置 Docker 映像的启动时间 ##

默认情况下，如果容器在 230 秒之前未启动，平台将重新启动容器。 如果自定义 Docker 映像在 230 秒之后启动，可以使用 `WEBSITES_CONTAINER_START_TIME_LIMIT` 应用设置，此设置的值以秒为单位，以便平台在重新启动容器之前使其继续保持运行。 默认值为 230 秒，允许的最大值为 600 秒。

## <a name="how-to-unmount-the-platform-provided-storage"></a>如何：卸载平台提供的存储 ##

默认情况下，该平台将永久存储共享装载到 `\home\` 目录。 如果容器映像不需要永久共享，则可以通过将 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 应用设置为 `false` 来禁用安装此存储。 你仍可以从 scm 站点访问该存储，并且所有 Docker 日志（如果已启用）都将写入到平台生成的日志文件。

## <a name="how-to-switch-back-to-using-a-built-in-image"></a>如何：向后切换以重新使用内置映像 ##

从使用自定义映像切换到使用内置映像：

1. 在 [Azure 门户](https://portal.azure.com)中找到 Linux Web 应用，并在“设置”中单击“应用服务”。

2. 选择“运行时堆栈”以用于内置映像，并单击“保存”。 

![配置内置 Docker 映像][5]


## <a name="troubleshooting"></a>故障排除 ##

如果未能以自定义 Docker 映像启动应用程序，请检查 LogFiles 目录中的 Docker 日志。 可通过 SCM 站点或 FTP 访问此目录。
若要从容器记录 `stdout` 和 `stderr`，需要在“诊断记录”下启用“Docker 容器记录”。

![启用日志记录][8]

![使用 Kudu 查看 Docker 日志][7]

可从“开发工具”菜单中的“高级工具”访问 SCM 站点。

## <a name="next-steps"></a>后续步骤 ##

单击以下链接，开始使用 Linux Web 应用。   

* [Linux 上的 Azure Web 应用简介](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](./app-service-linux-using-nodejs-pm2.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)

如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

