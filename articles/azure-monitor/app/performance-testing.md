---
title: Azure 应用程序 Insights 的性能和负载测试 |Microsoft Docs
description: 通过 Azure 应用程序 Insights 设置性能和负载测试
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669856"
---
# <a name="performance-testing"></a>性能测试

> [!NOTE]
> 基于云的负载测试服务已弃用。 可在[此处](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)找到有关弃用、服务可用性和备用服务的详细信息。

Application Insights 允许你为网站生成负载测试。 与[可用性测试](monitor-web-app-availability.md)一样，你可以从世界各地的 Azure 测试代理发送基本请求或[多步骤请求](availability-multistep.md)。 性能测试允许用户60最多模拟20000个用户。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要创建性能测试，首先需要创建一个 Application Insights 资源。 如果已创建资源，请转到下一节。

从 "Azure 门户中，选择"**创建资源**" > **开发人员工具** > " **Application Insights**并创建 Application Insights 资源。

## <a name="configure-performance-testing"></a>配置性能测试

如果这是你第一次创建性能测试，请选择 "**设置组织**"，并选择 Azure DevOps 组织作为性能测试的源。

在 "**配置**" 下，单击 "**性能测试**"，然后单击 "**新建**" 以创建一个测试。

![至少填写网站的 URL](./media/performance-testing/new-performance-test.png)

若要创建基本性能测试，请选择测试类型 "**手动测试**"，并为测试填写所需的设置。

|设置| 最大值
|----------|------------|
| 用户负载 | 20,000 |
| 持续时间（分钟）  | 60 |  

创建测试后，单击 "**运行测试**"。

测试完成后，你将看到类似于下面的结果：

![测试结果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>配置 Visual Studio web 测试

Application Insights 高级性能测试功能构建在 Visual Studio 性能和负载测试项目之上。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>后续步骤

* [多步骤 web 测试](availability-multistep.md)
* [Url ping 测试](monitor-web-app-availability.md)
