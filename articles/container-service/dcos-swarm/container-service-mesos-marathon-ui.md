---
title: "使用 Marathon API 管理 Azure DC/OS 群集"
description: "使用 Marathon Web UI 将容器部署到 Azure 容器服务群集服务。"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b1c5fc223105b1dae0ce07f242a6b42b34fd2ab3
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>通过 Marathon Web UI 管理 Azure 容器服务 DC/OS 群集

DC/OS 提供了一种环境，可进行群集工作负荷的部署和缩放，以及底层硬件的抽象化。 DC/OS 上方是一种管理计划和执行计算工作负荷的框架。

尽管框架可用于许多常见的工作负荷，本文档介绍了如何开始使用 Marathon 部署容器。 


## <a name="prerequisites"></a>先决条件
开始了解这些示例之前，需要一个在 Azure 容器服务中配置的 DC/OS 群集。 还需要具有到此群集的远程连接。 有关这些项目的详细信息，请参阅以下文章：

* [部署 Azure 容器服务群集](container-service-deployment.md)
* [Connect to an Azure Container Service cluster](../container-service-connect.md)

> [!NOTE]
> 本文假定要通过本地端口 80 以隧道形式连接到 DC/OS 群集中。
>

## <a name="explore-the-dcos-ui"></a>探索 DC/OS UI
[建立](../container-service-connect.md)安全外壳 (SSH) 隧道后，请浏览到 http://localhost/。 这会加载 DC/OS Web UI 并显示有关群集的信息，如使用的资源、活动代理和正在运行的服务。

![DC/OS UI](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>探索 Marathon UI
若要查看 Marathon UI，请浏览到 http://localhost/marathon。 在此屏幕中，可以在 Azure 容器服务 DC/OS 群集上启动新容器或其他应用程序。 还可以查看有关正在运行的容器和应用程序的信息。  

![Marathon UI](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>部署 Docker 格式容器
要使用 Marathon 部署新容器，请单击“创建应用程序”，并在窗体选项卡中输入以下信息：

| 字段 | 值 |
| --- | --- |
| ID |nginx |
| 内存 | 32 |
| 映像 |nginx |
| 网络 |桥接 |
| 主机端口 |80 |
| 协议 |TCP |

![新应用程序 UI - 常规](./media/container-service-mesos-marathon-ui/dcos4.png)

![新应用程序 UI - Docker 容器](./media/container-service-mesos-marathon-ui/dcos5.png)

![新应用程序 UI - 端口和服务发现](./media/container-service-mesos-marathon-ui/dcos6.png)

如果想以静态方式将容器端口映射到代理端口，则需要使用 JSON 模式。 为此，请使用切换功能将“新建应用程序”向导切换到“JSON 模式”。 然后在应用程序定义的 `portMappings` 节下输入以下设置。 此示例将容器的端口 80 绑定到 DC/OS 代理的端口 80。 进行更改后，可切换向导退出 JSON 模式。

```none
"hostPort": 80,
```

![新应用程序 UI - 端口 80 示例](./media/container-service-mesos-marathon-ui/dcos13.png)

如果想要启用运行状况检查，请在“运行状况检查”选项卡中设置路径。

![新应用程序 UI - 运行状况检查](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

使用专用和公用代理组部署 DC/OS 群集。 为使群集能从 Internet 访问应用程序，需将应用程序部署到公用代理。 为此，请选择“新建应用程序”向导上的“可选”选项卡，并输入 **slave_public** 作为“已接受资源角色”。

然后单击“创建应用程序”。

![新应用程序 UI - 公用代理设置](./media/container-service-mesos-marathon-ui/dcos14.png)

返回 Marathon 主页，会看到容器的部署状态。 最初会显示“正在部署”状态。 成功部署后，状态将更改为“正在运行”。

![Marathon 主页 UI - 容器部署状态](./media/container-service-mesos-marathon-ui/dcos7.png)

切换回 DC/OS Web UI (http://localhost/) 后，会看到任务在 DC/OS 群集上运行，此情况下为 Docker 格式容器任务。

![DC/OS Web UI - 任务在群集上运行](./media/container-service-mesos-marathon-ui/dcos8.png)

若要查看运行任务的群集节点，请单击“节点”选项卡。

![DC/OS Web UI - 任务群集节点](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>访问容器

在此示例中，应用程序运行于公共代理节点上。 通过 Internet 访问应用程序，具体方法是浏览群集的代理 FQDN：`http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`，其中：

* **DNSPREFIX** 是部署群集时提供的 DNS 前缀。
* **REGION** 是资源组所在的区域。

    ![来自 Internet 的 Nginx](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>后续步骤
* [使用 DC/OS 和 Marathon API](container-service-mesos-marathon-rest.md)

* 深入了解 Azure 容器服务和 Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
