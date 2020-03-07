---
title: 教程 - 在 Azure 上创建 Jenkins 服务器
description: 本教程将通过 Jenkins 解决方案模板在 Azure Linux 虚拟机上安装 Jenkins，并生成示例 Java 应用程序。
keywords: jenkins, azure, devops, 门户, 虚拟机, 解决方案模板
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274526"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>教程：在 Azure Linux VM 上创建 Jenkins 服务器 

本教程演示如何在 Ubuntu Linux VM 上安装 [Jenkins](https://jenkins.io)，其工具和插件配置为支持 Azure。 完成后，你会有一个在 Azure 中运行的 Jenkins 服务器，并可生成 [GitHub](https://github.com) 中的示例 Java 应用。

> [!div class="checklist"]
> * 在 Azure 上安装和配置 Jenkins 服务器
> * 使用 SSH 隧道访问 Jenkins 控制台
> * 创建自由风格项目
> * 编译代码并打包示例应用

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]