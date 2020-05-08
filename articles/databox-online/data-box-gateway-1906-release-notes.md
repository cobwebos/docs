---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 发行说明 |Microsoft Docs
description: 描述运行1906版本的 Azure Data Box Gateway 和 Azure Data Box Edge 的关键打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 9f4633a3fcafcb2b3151270715036e7940eb1c91
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561848"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge 和 Azure Data Box Gateway 1906 发行说明

以下发行说明针对 Azure Data Box Edge 和 Azure Data Box Gateway 的1906版本确定了严重的打开问题和解决的问题。 

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Data Box Edge/Data Box Gateway 之前，请仔细查看发行说明中包含的信息。

此版本对应于软件版本：

- **Data Box Gateway 1906 （1.6.978.743）**
- **Data Box Edge 1906 （1.6.978.743）**

> [!NOTE]
> 更新1906只能应用于运行正式发行版（GA）的 Data Box Edge 设备或软件的1905版本。

## <a name="whats-new"></a>新增功能

- **恢复密钥管理工作流中的 Bug 修复**-在早期版本中，存在一个 bug 由于，其中未应用恢复密钥。 在此版本中已修复这一 bug。 我们强烈建议你将此更新应用为恢复密钥，以便在设备不启动的情况下恢复设备上的数据。 有关详细信息，请参阅如何[在部署 Data Box Edge 或 Data Box Gateway 时保存恢复密钥](azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- **现场可编程入口阵列（FPGA）日志记录改进**-已建立与 FPGA 相关的1905版本、日志记录和警报增强功能。 如果使用 FPGA 的 Edge 计算功能，这将继续作为 Data Box Edge 的必需更新。 有关详细信息，请参阅如何[在 Data Box Edge 上通过 Edge 计算转换数据](azure-stack-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>GA 版本中的已知问题

对于此版本，不会记录任何新问题。 所有已记录版本的问题已从以前的版本中继续执行。 若要查看已知问题的列表，请参阅[GA 版本中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [准备部署 Azure Data Box Edge](azure-stack-edge-deploy-prep.md)
