---
title: 启用对 Avere vFXT 的支持 - Azure
description: 了解如何从 Avere vFXT for Azure 启用支持上传
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: b975f84106507da6adff11dc62441526773f5cab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998135"
---
# <a name="enable-support-uploads"></a>启用支持上传

Avere vFXT for Azure 可以自动上传关于你的群集的支持数据。 这些上传使得支持人员能够提供尽可能最好的客户服务。

## <a name="steps-to-enable-uploads"></a>启用上传的步骤

遵循以下步骤从 Avere 控制面板来激活支持。 （若要了解如何打开 Avere 控制面板，请阅读[访问 vFXT 群集](avere-vfxt-cluster-gui.md)。）

1. 导航到顶部的“设置”选项卡。
1. 单击左侧的“支持”链接并接受隐私政策。

   ![显示 Avere 控制面板和弹出窗口的屏幕截图，其中包含用于接受隐私策略的“确认”按钮](media/avere-vfxt-privacy-policy.png)

1. 单击“客户信息”左侧的三角形以展开该部分。
1. 单击“重新验证上传信息”按钮。
1. 在“唯一群集名称”中设置群集的支持名称，确保它可以唯一地向支持人员标识你的群集。
1. 选择与“统计信息监视”、“常规信息上传”和“故障信息上传”对应的复选框。
1. 单击“提交”。

   ![包含支持设置页的完整客户信息部分的屏幕截图](media/avere-vfxt-support-info.png)

1. 单击“安全主动支持 (SPS)”左侧的三角形以展开该部分。
1. 单击“启用 SPS 链接”的复选框。
1. 单击“提交”。

   ![包含支持设置页上的完整“安全主动支持”部分的屏幕截图](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>后续步骤

如果需要将本地存储系统添加到群集，或更新新创建的 Blob 容器中的默认加密密钥，请按照[配置存储](avere-vfxt-add-storage.md)中的说明操作。 

如果你已准备好开始将客户端连接到群集，请阅读[装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)。