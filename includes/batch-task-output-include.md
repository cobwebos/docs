---
title: include 文件
description: include 文件
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797152"
---
在 Azure Batch 中运行的任务可能在其运行时产生输出数据。 通常需要存储任务输出数据，以便作业中的其他任务和/或执行该作业的客户端应用程序进行检索。 任务可向 Batch 计算节点的文件系统写入输出数据，但当重置节点映像或节点离开池时，节点上的所有数据都会丢失。 任务可能还具有文件保留期，超过此保留期后，系统将删除该任务创建的文件。 出于这些原因，请务必将以后需要的任务输出保留到数据存储，如 [Azure 存储](https://docs.microsoft.com/azure/storage/)。

有关 Batch 中的存储帐户选项，请参阅 [Batch 帐户和 Azure 存储帐户](../articles/batch/accounts.md#azure-storage-accounts)。
