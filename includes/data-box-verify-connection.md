---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67173617"
---
按照以下步骤连接到存储帐户并验证连接。

1. 在存储资源管理器中，打开“连接到 Azure 存储”  对话框。 在“连接到 Azure 存储”对话框中，选择“使用存储帐户名称和密钥”。  

    ![Data Box 仪表板](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. 粘贴**帐户名称**和**帐户密钥**（本地 Web UI 的“连接并复制”  页面中的“密钥 1”值）。 选择“其他(在下面输入)”  作为存储终结点域，然后如下所述提供 blob 服务终结点。 只有当通过 *http* 进行传输时才应选中“使用 HTTP”选项。 如果使用 *https*，则不要选中此选项。 选择“**下一页**”。

    ![Data Box 仪表板](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. 在“连接摘要”  对话框中，复查所提供的信息。 选择“连接”  。

    ![Data Box 仪表板](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. 成功添加的帐户显示在存储资源管理器的左窗格中，并且其名称的末尾追加有 (外部, 其他)。 单击“Blob 容器”  以查看容器。

    ![Data Box 仪表板](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
