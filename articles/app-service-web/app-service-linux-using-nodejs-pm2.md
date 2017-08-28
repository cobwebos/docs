---
title: "在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置 | Microsoft Docs"
description: "在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置"
keywords: "azure 应用服务, web 应用, nodejs, pm2, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7ad48d42f8cc847ece199a2372c20430c4c8424e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="use-pm2-configuration-for-nodejs-in-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


如果将应用程序堆栈设置为适用于 Linux 上 Azure Web 应用的 Node.js，则可选择设置 Node.js 启动文件，如下图所示：

![设置 Node.js 启动文件][1]

可以使用此选项执行以下任务之一：

* 指定 Node.js 应用的启动脚本（例如：/bin/server.js）。
* 指定用于 Node.js 应用的 PM2 配置文件（例如：/foo/process.json）。
  
  > [!NOTE]
  > 如果要在修改某些文件时使 Node.js 进程自动重启，可使用 PM2 配置。 否则，当应用程序收到更改通知时（例如当应用程序代码更改时）不会重启。
  > 
  > 

可以查看 Node.js [进程文件文档](http://pm2.keymetrics.io/docs/usage/application-declaration/)以了解所有选项，也可将下面的示例用作 process.json 文件：

        {
          "name"        : "worker",
          "script"      : "./bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["./bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

此配置中的重要注意事项如下：

* “script”属性指定应用程序的启动脚本。
* “instances”属性指定需要启动多少个节点进程的实例。 若要在具有多个核心的较大 VM 上运行应用程序，最好在此处设置较大的值，将资源最大化。
* “watch”数组指定在文件进行更改时需要重新启动节点进程的所有文件。
* 对于“watch_options”，考虑到应用程序内容的装入方式，目前需将“usePolling”指定为 true。

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](app-service-linux-intro.md)
* [Linux 上的 Azure App Service Web 应用常见问题解答](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png

