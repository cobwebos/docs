---
title: 使用 Azure Application Insights 进行性能和负载测试 | Microsoft Docs
description: 使用 Azure Application Insights 设置性能和负载测试
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669856"
---
# <a name="performance-testing"></a>性能测试

> [!NOTE]
> 基于云的负载测试服务已弃用。 有关弃用、服务可用性和替代服务的更多信息，请参见[此处](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)。

使用 Application Insights 可以为网站生成负载测试。 与[可用性测试](monitor-web-app-availability.md)一样，你可以从世界各地的 Azure 测试代理发送基本请求或[多步骤请求](availability-multistep.md)。 性能测试允许你在长达 60 分钟的时间内模拟多达 20,000 个并发用户。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要创建性能测试，首先需创建 Application Insights 资源。 如果已创建资源，请转到下一部分。

在 Azure 门户中选择“创建资源”   > “开发人员工具”   > “Application Insights”  ，创建一个 Application Insights 资源。

## <a name="configure-performance-testing"></a>配置性能测试

如果这是你第一次创建性能测试，请选择“设置组织”  ，然后选择 Azure DevOps 组织作为性能测试的源。

在“配置”  下，转到“性能测试”  并单击“新建”  以创建测试。

![至少填写网站的 URL](./media/performance-testing/new-performance-test.png)

若要创建基本性能测试，请选择“手动测试”  测试类型，并填写测试所需的设置。

|设置| 最大值
|----------|------------|
| 用户负载 | 20,000 |
| 持续时间（分钟）  | 60 |  

创建测试后，单击“运行测试”  。

测试完成后，你将看到与以下结果类似的结果：

![测试结果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>配置 Visual Studio Web 测试

Application Insights 高级性能测试功能在 Visual Studio 性能和负载测试项目的基础上构建。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>后续步骤

* [多步骤 Web 测试](availability-multistep.md)
* [URL ping 测试](monitor-web-app-availability.md)
