---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "58114925"
---
如果需要域，可以直接在 [Azure 管理门户](https://portal.azure.com)中购买域。 请使用以下步骤来购买域名，然后将其分配给 Web 应用。

1. 在浏览器中打开 [Azure 管理门户](https://portal.azure.com)。
2. 在“Web 应用”选项卡中，单击 Web 应用的名称，选择“设置”，并选择“自定义域和 SSL”。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. 在“自定义域和 SSL”边栏选项卡中，单击“购买域”。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. 在“购买域”边栏选项卡中，使用文本框输入想要购买的域名。 将直接在文本框下方显示建议的可用域。 选择你想要购买什么的域。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. 单击“联系人信息”，并填充域的联系人信息表单。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. 单击“购买域”边栏选项卡上的“选择”，就会在“购买确认”边栏选项卡上看到购买信息。 如果接受法律条款，请单击“购买”，并就会提交订单，可以在“通知”中监视购买流程。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. 如果成功订购了域，则可管理该域并将其分配给 Web 应用。 单击域右侧的“...”。 然后，你可以**取消购买**或**管理域**。 单击“管理域”，然后可以在“管理域”边栏选项卡中将子域绑定到 Web 应用。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    配置完成后，自定义域名会列在 Web 应用的“主机名绑定”部分。

此时，应该可以在浏览器中输入自定义域名，并查看它是否成功地你将转至 Web 应用。

