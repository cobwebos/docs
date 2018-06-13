---
title: 在 Azure 容器实例上运行的容器中执行命令
description: 了解如何在 Azure 容器实例上当前正在运行的容器中执行命令
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.openlocfilehash: 43211f620efb16cbcd722d3d386b1bb862fc6280
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165640"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>在运行的 Azure 容器实例中执行命令

Azure 容器实例支持在运行的容器中执行命令。 在应用程序开发和故障排除期间，在已启动的容器中运行命令特别有用。 此功能的最常见用途是启动交互式 shell，以便能够在运行的容器中调试问题。

## <a name="run-a-command-with-azure-cli"></a>使用 Azure CLI 运行命令

在 [Azure CLI][azure-cli] 中使用 [az container exec][az-container-exec] 在运行的容器中执行命令：

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

例如，若要在 Nginx 容器中启动 Bash shell：

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

在以下示例输出中，Bash shell 已在运行的 Linux 容器中启动，提供一个可用于执行 `ls` 的终端：

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

在此示例中，命令提示符已在运行的 Nanoserver 容器中启动：

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>多容器组

如果[容器组](container-instances-container-groups.md)包含多个容器（例如应用程序容器和日志记录分支），请使用 `--container-name` 指定要在其中运行命令的容器的名称。

例如，容器组 *mynginx* 包含两个容器：*nginx-app* 和 *logger*。 若要在 *nginx-app* 容器中启动 shell：

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>限制

Azure 容器实例目前支持使用 [az container exec][az-container-exec] 启动单个进程；不能传递命令参数。 例如，不能像 `sh -c "echo FOO && echo BAR"` 一样串接命令，或执行 `echo FOO`。

## <a name="next-steps"></a>后续步骤

在[排查 Azure 容器实例中的容器和部署问题](container-instances-troubleshooting.md)中了解其他故障排除工具和常见部署问题。

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure