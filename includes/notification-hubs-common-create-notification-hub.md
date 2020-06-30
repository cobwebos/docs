---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095187"
---
### <a name="create-a-notification-hub"></a>创建通知中心 

本部分将创建一个通知中心，并使用 APNS 配置身份验证。 可以使用 p12 推送证书或基于令牌的身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

1. 登录到 [Azure](https://portal.azure.com)。

1. 单击“创建资源”，搜索并选择“通知中心”，然后单击“创建”  。

1. 更新以下字段，然后单击“创建”：

    **基本详细信息**  

    **订阅：** 从下拉列表中选择目标订阅  
    **资源组：** 新建资源组（或选择现有资源组）  

    **命名空间详细信息**  

    **通知中心命名空间：** 输入通知中心命名空间的全局唯一名称  

    > [!NOTE]
    > 确保为此字段选择“新建”选项。

    **通知中心详细信息**  

    **通知中心：** 输入通知中心的名称  
    **位置：** 从下拉列表中选择合适的位置  
    **定价层：** 保留默认“免费”选项  

    > [!NOTE]
    > 除非已达到免费层的最大中心数。

1. 预配通知中心后，导航到该资源。
1. 导航到新的通知中心。
1. 从列表中选择“访问策略”（在“管理”下） 。
1. 请记下策略名称值及其对应的连接字符串值 。
