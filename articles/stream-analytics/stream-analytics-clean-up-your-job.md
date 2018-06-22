---
title: 清理 Azure 流分析作业
description: 本文指导如何删除 Azure 流分析作业。
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659268"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>清理 Azure 流分析作业

可以通过Azure 门户、Azure PowerShell、Azure SDK for .NET 或 REST API 轻松地删除 Azure 流分析作业。

>[!NOTE] 
>当停止流分析作业时，数据只会保留在输入和输出存储中，例如事件中心或 Azure SQL 数据库。 如果需要从 Azure 中删除数据，请务必遵循流分析作业的输入和输出资源删除过程进行操作。

## <a name="stop-a-job-in-azure-portal"></a>停止 Azure 门户中的作业

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 找到正在运行的流分析作业并选择它。

3. 在流分析作业页上，选择“停止”以停止作业。 

   ![停止作业](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>删除 Azure 门户中的作业

1. 登录到 Azure 门户。 

2. 找到现有流分析作业并选择它。

3. 在流分析作业页上，选择“删除”以删除作业。 

   ![删除作业](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>使用 PowerShell 停止或删除作业

若要使用 PowerShell 停止作业，请使用 [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) cmdlet。 若要使用 PowerShell 删除作业，请使用 [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) cmdlet。

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>使用 Azure SDK for .NET 停止或删除作业

若要使用 Azure SDK for .NET 停止作业，请使用 [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) 方法。 若要使用 Azure SDK for .NET 删除作业，请使用 [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) 方法。

## <a name="stop-or-delete-a-job-using-rest-api"></a>使用 REST API 停止或删除作业

若要使用 REST API 停止作业，请参阅[停止](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop)方法。 若要使用 REST API 删除作业，请参阅[删除](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete)方法。