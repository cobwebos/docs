---
title: 教程：在 Azure Spring Cloud 中缩放应用程序 | Microsoft Docs
description: 本教程介绍如何在 Azure 门户的 Azure Spring Cloud 中缩放应用程序
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132906"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>教程：在 Azure Spring Cloud 中缩放应用程序

本教程演示如何在 Azure 门户中使用 Azure Spring Cloud 仪表板缩放任何微服务应用程序。 上下缩放应用程序，方法是：修改虚拟 CPU (vCPU) 的数目和内存的容量。 水平缩放应用程序，方法是：修改应用程序实例的数目。 完成后，你会了解如何对服务中的每个应用程序进行快速的手动调整。 缩放在数秒内生效，不需任何代码更改或重新部署。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 部署的 Azure Spring Cloud 服务实例。  按[快速入门](spring-cloud-quickstart-launch-app-cli.md)的说明开始操作。
* 至少已在该服务实例中创建一个应用程序。


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>导航到 Azure 门户中的“缩放”页

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Spring Cloud 的“概览”页。 

1. 选择包含服务的资源组。

1. 在左侧菜单中转到“设置”标题下的“应用”选项卡。  

1. 选择要缩放的应用程序。 在此示例中，我们将缩放名为“account-service”的应用程序。 此时会转到应用程序的“概览”页。 

1. 在左侧菜单中转到“设置”标题下的“缩放”选项卡。   你应会看到以下部分中所示的缩放属性的选项。

## <a name="scale-your-application"></a>缩放应用程序

可以修改缩放属性。 请记住以下注意事项。

* **CPU**：允许的最大 CPU 数是每个应用程序实例 4 个。 一个应用程序的总 CPU 数将是此处设置的值乘以应用程序实例数。

* **内存/GB**：允许的最大内存量是每个应用程序实例 8GB。  一个应用程序的总内存量将是此处设置的值乘以应用程序实例数。

* **应用实例计数**：可以在“标准”层中横向扩展到 20 个实例。 此值更改微服务应用程序的单独运行实例的数目。

确保单击“保存”按钮，以应用缩放设置。 

![Azure 门户中的缩放服务](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

数秒钟后，所做的更改会显示在“概览”页中，更多详细信息在“应用程序实例”选项卡中提供。   缩放不需任何代码更改或重新部署。

## <a name="next-steps"></a>后续步骤

在本教程中，我们了解了如何手动缩放 Azure Spring Cloud 应用程序。  若要了解如何监视应用程序，请继续学习下一教程。

> [!div class="nextstepaction"]
> [了解如何监视应用程序](spring-cloud-tutorial-distributed-tracing.md)
