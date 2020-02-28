---
title: 编写代码来使用 Azure Application Insights 跟踪请求 | Microsoft Docs
description: 编写代码来使用 Application Insights 跟踪请求以便可以获取你的请求的配置文件。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671590"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>编写代码来使用 Application Insights 跟踪请求

若要在“性能”页上查看应用程序的配置文件，Application Insights 需要跟踪针对该应用程序的请求。 Application Insights 可以自动跟踪针对基于已检测的框架构建的应用程序的请求。 两个示例是 ASP.NET 和 ASP.NET Core。 

对于其他应用程序，例如 Azure 云服务辅助角色和 Service Fabric 无状态 API，你需要编写代码以告知 Application Insights 请求开始和结束的位置。 编写此代码后，请求遥测数据将发送到 Application Insights。 可以在“性能”页上查看遥测数据并为这些请求收集配置文件。 

若要手动跟踪请求，请执行以下操作：

  1. 在应用程序生命周期的早期添加以下代码：  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      有关此全局检测密钥配置的详细信息，请参阅 [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)（结合使用 Service Fabric 和 Application Insights）。  

  1. 对于想要检测的任何代码片段，请在其两侧添加 `StartOperation<RequestTelemetry>` **using**语句，如以下示例中所示：

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        不支持在其他 `StartOperation<RequestTelemetry>` 作用域中调用 `StartOperation<RequestTelemetry>`。 可以改为在嵌套作用域中使用 `StartOperation<DependencyTelemetry>`。 例如：  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
