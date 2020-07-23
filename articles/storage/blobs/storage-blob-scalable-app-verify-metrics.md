---
title: 在 Azure 门户中验证存储帐户的吞吐量和延迟指标 | Microsoft Docs
description: 了解如何在门户中验证存储帐户的吞吐量和延迟指标。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: cfc3c445595bec046de4256a57d566067fe3d29e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692445"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>验证存储帐户的吞吐量和延迟指标

本教程是一个系列的第 4 部分，也是该系列的最后一部分。 在前面的教程中，已学习如何将大量随机数据上传和下载到 Azure 存储帐户。 本教程演示如何在 Azure 门户中使用指标查看吞吐量和延迟。

该系列的第 4 部分中介绍了如何：

> [!div class="checklist"]
> * 在 Azure 门户中配置图表
> * 验证吞吐量和延迟指标

[Azure 存储指标](../common/monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)使用 Azure Monitor 提供用于深入了解存储帐户的性能和可用性的统一视图。

## <a name="configure-metrics"></a>配置指标

导航到存储帐户中“设置”下的“指标(预览)”   。

从“子服务”  下拉列表中选择 Blob。

在“指标”  下，选择下表中的指标之一：

可通过以下指标了解应用程序的延迟和吞吐量。 在门户中配置的指标在 1 分钟的平均值内。 如果事务在一分钟的中间完成，则该分钟的数据是平均值减半。 在属于你应用程序的一个或多个进程和/或一个或多个计算机中，如果内存消耗量在较长的时间段（几个小时）内持续增加，将发出一个典型的通知。 可以通过将此信息与门户指标结合使用来全面了解吞吐量。

|指标|定义|
|---|---|
|**成功 E2E 延迟**|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|
|**成功服务器延迟**|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。 |
|**中的**|向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求数，以及引发错误的请求数。 在该示例中，块大小设置为 100 MB。 在这种情况下，每个 100 MB 的块被认为是一个事务。|
|**流入量**|流入数据量。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 |
|**流出量**|流出数据量。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。 |

在“时间”  旁边选择“过去 24 小时(自动)”  。 针对“时间粒度”  选择“过去一小时”  和“分钟”  ，然后单击“应用”  。

![存储帐户指标](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

图表可以分配有多个指标，但分配多个指标将禁用按维度分组功能。

## <a name="dimensions"></a>维度

可使用[维度](../common/monitor-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions)深入了解图表和获取更多详细信息。 不同指标具有不同维度。 可用的一个维度是“API 名称”  维度。 此维度将图表拆分为每个单独的 API 调用。 下面的第一个图显示存储帐户的总事务数的示例图表。 第二个图显示相同图表，但选中了“API 名称”维度。 如你所见，列出了每个事务，从而让你详细了解按 API 名称进行了多少个调用。

![存储帐户指标 - 不带维度的事务](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![存储帐户指标 - 事务](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请选择适用于 VM 的资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本系列教程的第四部分中，已学习了如何查看示例解决方案的指标，例如如何：

> [!div class="checklist"]
> * 在 Azure 门户中配置图表
> * 验证吞吐量和延迟指标

请访问以下链接，查看预先生成的存储示例。

> [!div class="nextstepaction"]
> [Azure 存储脚本示例](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md
