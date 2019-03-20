---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: cd18d71d26410767a2d3119c12a1339bdc84bd33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114291"
---
1. 在配置服务器上启动 CSPSConfigtool.exe。 桌面上已提供该工具的快捷方式，在 *安装位置*\home\svsystems\bin 文件夹中也可以找到它。
2. 单击“管理帐户” > “添加帐户”。

    ![添加帐户](./media/site-recovery-add-vcenter-account/credentials1.png)
3. 在“帐户详细信息”中，添加用于自动发现的帐户。

    ![详细信息](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
   > 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请单击“配置服务器” > ***服务器名称*** > “刷新服务器”。
