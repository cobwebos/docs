---
title: 编写代码来使用 Azure Application Insights 跟踪请求 | Microsoft Docs
description: 编写代码来使用 Application Insights 跟踪请求以便可以获取你的请求的配置文件
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081575"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>编写代码来使用 Application Insights 跟踪请求

若要在“性能”页面上查看你的应用程序的配置文件，Application Insights 需要跟踪针对你的应用程序的请求。 Application Insights 可以自动跟踪针对基于已检测的框架（例如 ASP.net 和 ASP.Net Core）构建的应用程序发出的请求。 但是，对于其他应用程序，例如 Azure 云服务辅助角色和 Service Fabric 无状态 API，你需要编写代码来告诉 Application Insights 你的请求从哪里开始在哪里结束。 在编写此代码后，请求遥测数据将被发送到 Application Insights，你可以在“性能”页面上看到遥测数据，并且将针对那些请求收集配置文件。 

下面是手动跟踪请求时需要采取的步骤：


  1. 在应用程序生命周期的早期添加以下代码：  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      有关此全局检测密钥配置的详细信息，请参阅 [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)（结合使用 Service Fabric 和 Application Insights）。  

  1. 对于想要检测的任何代码片段，请在其两侧添加 `StartOperation<RequestTelemetry>` USING 语句，如以下示例所示：

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
