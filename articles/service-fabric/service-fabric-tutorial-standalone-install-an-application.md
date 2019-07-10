---
title: 教程：在独立 Service Fabric 群集上安装应用程序 - Azure Service Fabric | Microsoft Docs
description: 本教程介绍如何将应用程序安装到独立 Service Fabric 群集中。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 58daa53dba8f18c3f73253008fe687ba8b0d0839
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274074"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>教程：在 Service Fabric 独立群集上部署应用程序

Service Fabric 独立群集为你提供选择自己的环境的选项，并创建群集作为 Service Fabric 所采用的“任何 OS、任何云”方法的一部分。 在本教程系列中，将创建一个在 AWS 上托管的独立群集并将应用程序部署到其中。

本教程是一个系列中的第三部分。  Service Fabric 独立群集为你提供选择自己的环境的选项，并使用 Service Fabric 创建群集作为“任何 OS、任何云”方法的一部分。 本教程介绍如何创建托管此独立群集所需的 AWS 基础结构。

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 下载示例应用
> * 部署到群集

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* [安装 Visual Studio 2019](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
* [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序

在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>将应用部署到 Service Fabric 群集

下载应用程序后，可以直接从 Visual Studio 将其部署到群集中。

1. 打开 Visual Studio

2. 选择“文件”   >   “打开”

3. 导航到将 git 存储库克隆到的文件夹，然后选择 Voting.sln

4. 在解决方案资源管理器中右键单击 `Voting` 应用程序项目，选择“发布” 

5. 选择**连接终结点**的下拉列表，并输入群集中某个节点的公共 DNS 名称。  例如，`ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000` 。 请注意，在 Azure 中，不会自动提供完全限定的域名 (FQDN)，但可以[在“VM 概述”页中轻松设置](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)。

6. 打开首选浏览器并键入群集地址（连接终结点，此应用在端口 8080 上部署 - 例如，ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080）。

    ![来自群集的 API 响应](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>后续步骤

本系列教程的第三部分介绍了如何将应用程序部署到群集：

> [!div class="checklist"]
> * 下载示例应用
> * 部署到群集

转到本系列教程的第四部分来清理群集。

> [!div class="nextstepaction"]
> [清理资源](service-fabric-tutorial-standalone-clean-up.md)