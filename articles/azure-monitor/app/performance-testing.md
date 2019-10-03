---
title: 性能和负载测试使用 Azure Application Insights |Microsoft Docs
description: 设置性能和负载测试使用 Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305168"
---
# <a name="performance-testing"></a>性能测试

> [!NOTE]
> 已弃用基于云的负载测试服务。 有关弃用、服务可用性和替代服务的更多信息，请参见[此处](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)。

Application Insights，可生成你的网站的负载测试。 像[可用性测试](monitor-web-app-availability.md)，你可以将任一基本请求发送或[多步骤请求](availability-multistep.md)从 Azure 测试在世界各地的代理。 性能测试中，您可以模拟最多 60 分钟最多 20,000 个用户同时使用。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要创建性能测试，首先需要创建 Application Insights 资源。 如果你已创建资源，请继续执行下一节。

从 Azure 门户中，选择**创建资源** > **开发人员工具** > **Application Insights**并创建 Application Insights资源。

## <a name="configure-performance-testing"></a>配置性能测试

如果这是你第一次创建性能测试选择**设置组织**和选择 Azure DevOps 组织为你的性能测试的源。

下**配置**，请转到**性能测试**然后单击**新建**来创建测试。

![至少填写网站的 URL](./media/performance-testing/new-performance-test.png)

若要创建一个基本性能测试，请选择的测试类型**手动测试**并填写所需的设置为你的测试。

|设置| 最大值
|----------|------------|
| 用户负载 | 20,000 |
| 持续时间 （分钟）  | 60 |  

创建你的测试后，单击**运行测试**。

测试完成后，你将看到类似于下面的结果的结果：

![测试结果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>配置 Visual Studio web 测试

Application Insights 高级的性能测试功能基于 Visual Studio 性能和负载测试项目。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>后续步骤

* [多步骤 web 测试](availability-multistep.md)
* [Url ping 测试](monitor-web-app-availability.md)