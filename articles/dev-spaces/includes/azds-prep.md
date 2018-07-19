---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990971"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>准备用于 Docker 和 Kubernetes 开发的代码
到目前为止，已有一个可以在本地运行的基本 Web 应用。 现在，将通过创建定义应用的容器以及将应用部署到 Kubernetes 的方式的资产来将其容器化。 使用 Azure Dev Spaces，可以很容易完成此任务： 

1. 启动 VS Code 并打开 `webfrontend` 文件夹。 （可以忽略任何默认提示以添加调试资产或还原项目。）
1. 在 VS Code 中打开集成终端（使用“视图”>“集成终端”菜单）。
1. 运行此命令（确保 **webfrontend** 是当前文件夹）：

    ```cmd
    azds prep --public
    ```

Azure CLI 的 `azds prep` 命令使用默认设置生成 Docker 和 Kubernetes 资产：
* `./Dockerfile` 描述应用的容器映像，以及如何在容器内生成和运行源代码。
* `./charts/webfrontend` 下面的 [Helm 图表](https://docs.helm.sh)描述如何将容器部署到 Kubernetes。

暂无必要了解这些文件的全部内容。 但是，值得指出的是，**相同的 Kubernetes 和 Docker 配置即代码资产可以从开发一直用到生产，从而在不同的环境中提供更好的一致性。**
 
名为 `./azds.yaml` 的文件也由 `prep` 命令生成，并且它是 Azure Dev Spaces 的配置文件。 它通过额外配置对 Docker 和 Kubernetes 项目进行了补充，从而在 Azure 中实现了迭代开发体验。
