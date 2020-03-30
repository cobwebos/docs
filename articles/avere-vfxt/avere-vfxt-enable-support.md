---
title: 启用对 Avere vFXT 的支持 - Azure
description: 了解如何从 Avere vFXT for Azure 启用支持上传
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415372"
---
# <a name="enable-support-uploads"></a>启用支持上传

Azure 的 Avere vFXT 可以自动上载有关群集的支持数据。 这些上传使得支持人员能够提供尽可能最好的客户服务。

## <a name="steps-to-enable-uploads"></a>启用上传的步骤

遵循以下步骤从 Avere 控制面板来激活支持。 （读取[访问 vFXT 群集](avere-vfxt-cluster-gui.md)以了解如何打开控制面板。

1. 导航到顶部的“设置”选项卡。****
1. 单击左侧的“支持”**** 链接并接受隐私政策。

   ![显示 Avere 控制面板和弹出窗口的屏幕截图，其中包含用于接受隐私策略的“确认”按钮](media/avere-vfxt-privacy-policy.png)

1. 在支持配置页上，通过单击左侧的三角形打开 **"客户信息**"部分。
1. 单击“重新验证上传信息”**** 按钮。
1. 在**唯一群集名称**中设置群集的支持名称。 确保此名称唯一标识群集以支持员工。
1. 选择与“统计信息监视”****、“常规信息上传”**** 和“故障信息上传”**** 对应的复选框。
1. 单击 **“提交”**。

   ![包含支持设置页的完整客户信息部分的屏幕截图](media/avere-vfxt-support-info.png)

1. 单击“安全主动支持 (SPS)”左侧的三角形**** 以展开该部分。
1. 单击“启用 SPS 链接”**** 的复选框。
1. 单击 **“提交”**。

   ![包含支持设置页上的完整“安全主动支持”部分的屏幕截图](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>后续步骤

如果需要向群集添加本地或现有云存储系统，请按照[配置存储](avere-vfxt-add-storage.md)中的说明操作。

如果你已准备好开始将客户端连接到群集，请阅读[装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)。
