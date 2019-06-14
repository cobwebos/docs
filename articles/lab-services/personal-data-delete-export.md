---
title: 如何从 Azure 开发测试实验室中删除和导出个人数据 | Microsoft Docs
description: 了解如何从 Azure 开发测试实验室服务中删除和导出个人数据，以帮助履行一般数据保护条例 (GDPR) 中规定的义务。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394858"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>从 Azure 开发测试实验室中导出或删除个人数据
本文提供从 Azure 开发测试实验室服务中删除和导出个人数据的步骤。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>开发测试实验室收集哪些个人数据？
开发测试实验室主要从用户收集两项个人数据， 分别是：用户电子邮件地址和用户对象 ID。 此信息至关重要，使该服务能够为实验室管理员和实验室用户提供服务内功能。

### <a name="user-email-address"></a>用户电子邮件地址
开发测试实验室使用用户电子邮件地址向实验室用户发送自动关机电子邮件通知。 该电子邮件告知用户其计算机即将关闭。 如果需要，用户可以推迟或跳过关机。 在实验室级别或 VM 级别配置电子邮件地址。

**在实验室级别设置电子邮件：**

![在实验室级别设置电子邮件](./media/personal-data-delete-export/lab-user-email.png)

**在 VM 级别设置电子邮件：**

![在 VM 级别设置电子邮件](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>用户对象 ID
开发测试实验室使用用户对象 ID 向实验室管理员显示每月成本趋势和资源成本信息。 这样，管理员便可以跟踪成本并管理其实验室的阈值。 

**当前日历月份的估计成本趋势：** 
![当前日历月份的估计成本趋势](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**资源的估计本月至今成本：** 
![资源的估计本月至今成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我们为何需要这些个人数据？
开发测试实验室服务需要使用个人数据才能正常运行。 该服务必须使用这些数据来提供关键功能。 如果针对用户电子邮件地址设置了保留策略，则从我们的系统中删除实验室用户的电子邮件地址后，他们不会及时收到自动关机电子邮件通知。 同样，如果根据保留策略删除了用户对象 ID，实验室管理员将无法查看其实验室中计算机的每月成本趋势和资源成本。 因此，只要用户的资源在实验室中保持活动状态，就需要一直保留此数据。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何让系统忘记我的个人数据？
如果实验室用户想要删除这些个人数据，可以删除实验室中的相应资源。 在用户删除个人数据后，开发测试实验室服务会将已删除的数据匿名化 30 天。

例如，如果你删除了 VM 或电子邮件地址，则删除该资源后，开发测试实验室服务会将此数据匿名化 30 天。 删除后保留 30 天的策略旨在确保向实验室管理员提供准确的每月成本预测。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何请求导出个人数据？
实验室用户可以请求导出开发测试实验室服务存储的个人数据。 若要请求导出，请在实验室的“概述”页上导航到“个人数据”选项。   选择“请求导出”按钮，开始在实验室管理员的存储帐户中创建可下载的 Excel 文件。  然后，可以联系实验室管理员查看此数据。

1. 在左侧菜单中选择“个人数据”。  

    ![“个人数据”页](./media/personal-data-delete-export/personal-data-page.png)
2. 选择包含实验室的**资源组**。

    ![选择资源组](./media/personal-data-delete-export/select-resource-group.png)
3. 在该资源组中选择**存储帐户**。
4. 在“存储帐户”页上，选择“Blob”。  

    ![选择“Blob”磁贴](./media/personal-data-delete-export/select-blobs-tile.png)
5. 在容器列表中选择名为 **labresourceusage** 的容器。

    ![选择 Blob 容器](./media/personal-data-delete-export/select-blob-container.png)
6. 选择与实验室同名的**文件夹**。 在此文件夹中可以看到与实验室中的**磁盘**和**虚拟机**对应的 **csv** 文件。 可以下载这些 csv 文件，筛选请求访问的实验室用户的内容，然后与他们共享这些内容。

    ![下载 CSV 文件](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
