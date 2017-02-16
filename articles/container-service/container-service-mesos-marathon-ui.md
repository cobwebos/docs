---
title: "使用 Marathon UI 管理 Azure DC/OS 群集 | Microsoft Docs"
description: "使用 Marathon Web UI 将容器部署到 Azure 容器服务群集服务。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: de250fbc3982cdc493f74976914290d8ac89bb08


---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>通过 Marathon Web UI 管理 Azure 容器服务 DC/OS 群集
DC/OS 提供了一种环境，可进行群集工作负荷的部署和缩放，以及底层硬件的抽象化。 DC/OS 上方是一种管理计划和执行计算工作负荷的框架。

尽管框架可用于许多常见的工作负荷，本文档介绍了如何通过使用 Marathon 创建和缩放容器部署。 


## <a name="prerequisites"></a>先决条件
开始了解这些示例之前，需要一个在 Azure 容器服务中配置的 DC/OS 群集。 还需要具有到此群集的远程连接。 有关这些项目的详细信息，请参阅以下文章：

* [部署 Azure 容器服务群集](container-service-deployment.md)
* [连接到 Azure 容器服务群集](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>探索 DC/OS UI
[建立](container-service-connect.md)安全外壳 (SSH) 隧道后，请浏览到 http://localhost/。 这会加载 DC/OS Web UI 并显示有关群集的信息，如使用的资源、活动代理和正在运行的服务。

![DC/OS UI](./media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>探索 Marathon UI
若要查看 Marathon UI，请浏览到 http://localhost/Marathon。 在此屏幕中，可以在 Azure 容器服务 DC/OS 群集上启动新容器或其他应用程序。 还可以查看有关正在运行的容器和应用程序的信息。  

![Marathon UI](./media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>部署 Docker 格式容器
若要使用 Marathon 部署新容器，请单击“创建应用程序”，然后在窗体选项卡中输入以下信息：

| 字段 | 值 |
| --- | --- |
| ID |nginx |
| 内存 | 32 |
| 映像 |nginx |
| 网络 |桥接 |
| 主机端口 |80 |
| 协议 |TCP |

![新应用程序 UI - 常规](./media/dcos/dcos4.png)

![新应用程序 UI - Docker 容器](./media/dcos/dcos5.png)

![新应用程序 UI - 端口和服务发现](./media/dcos/dcos6.png)

如果想以静态方式将容器端口映射到代理端口，则需要使用 JSON 模式。 为此，请使用切换功能将“新建应用程序”向导切换到“JSON 模式”  。 然后在应用程序定义的 `portMappings` 部分下输入以下设置。 此示例将容器的端口 80 绑定到 DC/OS 代理的端口 80。 进行更改后，可切换向导退出 JSON 模式。

```none
"hostPort": 80,
```

![新应用程序 UI - 端口 80 示例](./media/dcos/dcos13.png)

如果想要启用运行状况检查，请在“运行状况检查”选项卡中设置路径。

![新应用程序 UI - 运行状况检查](./media/dcos/dcos_healthcheck.png)

使用专用和公用代理组部署 DC/OS 群集。 为使群集能从 Internet 访问应用程序，需将应用程序部署到公用代理。 为此，请选择“新建应用程序”向导上的“可选”选项卡，然后输入 **slave_public** 作为“已接受资源角色”。

然后单击“创建应用程序”。

![新应用程序 UI - 公用代理设置](./media/dcos/dcos14.png)

返回 Marathon 主页，会看到容器的部署状态。 最初会显示“正在部署”状态。 成功部署后，状态将更改为“正在运行”。

![Marathon 主页 UI - 容器部署状态](./media/dcos/dcos7.png)

切换回 DC/OS Web UI (http://localhost/) 后，会看到任务在 DC/OS 群集上运行，此情况下为 Docker 格式容器任务。

![DC/OS Web UI - 任务在群集上运行](./media/dcos/dcos8.png)

若要查看运行任务的群集节点，请单击“节点”选项卡。

![DC/OS Web UI - 任务群集节点](./media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>缩放容器
可以使用 Marathon UI 来缩放容器的实例计数。 为此，请导航到“Marathon”页，选择想要缩放的容器，然后单击“缩放应用程序”。 在“缩放应用程序”对话框中，输入所需的容器实例数，然后单击“缩放应用程序”。

![Marathon UI -“缩放应用程序”对话框](./media/dcos/dcos10.png)

缩放操作完成后，会看到同一任务的多个实例遍布 DC/OS 代理。

![DC/OS Web UI 仪表板 - 任务跨代理分布](./media/dcos/dcos11.png)

![DC/OS Web UI - 节点](./media/dcos/dcos12.png)

## <a name="next-steps"></a>后续步骤
* [使用 DC/OS 和 Marathon API](container-service-mesos-marathon-rest.md)

* 深入了解 Azure 容器服务和 Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 



<!--HONumber=Jan17_HO4-->


