---
title: Azure 数据框网关& Azure 数据框边缘 1906 发行说明*微软文档
description: 介绍运行 1906 版本的 Azure 数据框网关和 Azure 数据框边缘的关键打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099481"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure 数据框边缘和 Azure 数据框网关 1906 发行说明

以下发行说明标识 Azure 数据框边缘和 Azure 数据框网关的 1906 版本的关键未决问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署数据框边缘/数据盒网关之前，请仔细查看发行说明中包含的信息。

此版本对应于软件版本：

- **数据盒网关 1906 （1.6.978.743）**
- **数据框边缘 1906 （1.6.978.743）**

> [!NOTE]
> 更新 1906 只能应用于运行软件的通用版 （GA） 或 1905 版本的数据盒边缘设备。

## <a name="whats-new"></a>新增功能

- **恢复密钥管理工作流中的 Bug 修复**- 在早期版本中，由于未应用恢复密钥，出现了一个 Bug。 在此版本中已修复这一 bug。 我们强烈建议您应用此更新，因为恢复密钥允许您在设备未启动的情况下恢复设备上的数据。 有关详细信息，请参阅在[部署数据框边缘或数据框网关时如何保存恢复密钥](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- **现场可编程门阵列 （FPGA） 日志记录改进**- 从 1905 年开始发布，进行了与 FPGA 相关的日志记录和警报增强。 如果将 Edge 计算功能与 FPGA 一起使用，则这仍然是数据框边缘所需的更新。 有关详细信息，请参阅如何在[数据框边缘上使用 Edge 计算转换数据](data-box-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>GA 版本中的已知问题

此版本未注意到任何新问题。 所有发布的注意到的问题都从以前的版本中延续过来。 要查看已知问题的列表，请访问[GA 版本中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [准备部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
