---
title: Azure 数据工厂中复制数据工具疑难解答
description: 了解如何对 Azure 数据工厂中复制数据工具问题进行故障排除。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388348"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Azure 数据工厂中复制数据工具疑难解答

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了 Azure 数据工厂中复制数据工具的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>错误代码：无法在复制数据工具中验证

- **症状**：在复制数据工具的第一步中，你遇到了 "无法验证" 的警告消息。
- **原因**：禁用所有第三方 cookie 时可能会发生这种情况。
- **解决方法**： 
    - 使用 Internet Explorer 或 Microsoft Edge 浏览器。
    - 如果使用的是 Chrome 浏览器，请按照下面的说明添加*microsoftonline.com*和*windows.net*的 cookie 例外。
        1.  打开 Chrome 浏览器。
        2.  单击右侧的扳手或三行（自定义并控制 Google Chrome）。
        3.  单击“设置”。
        4.  在 "高级设置" 下搜索**cookie**或中转到**隐私**。
        5.  选择 "**内容设置**"。    
        6.  Cookie 应该设置为**允许设置本地数据（推荐）**。
        7.  单击 "**管理例外**"。 在 "**主机名模式**" 下，输入以下各项，并确保 "**允许**" 是行为集。
            - login.microsoftonline.com
            - login.windows.net
        8.  关闭浏览器并重新启动。
    - 如果使用的是 Firefox 浏览器，请按照以下说明添加 Cookie 例外。
        1. 从 Firefox 菜单中转到 "**工具**" "  >  **选项**"。
        2. 在 "**隐私**  >  **历史记录**" 下，你可能会看到当前设置为 "**使用历史记录的自定义设置**"。
        3. 在**Accept 第三方 cookie**中，你当前的设置可能会**永远不**会，你应该单击右侧的 "**例外**" 添加以下站点。
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  关闭浏览器并重新启动。 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>错误代码：无法打开登录页并输入密码

- **症状**：复制数据工具会将您重定向到登录页，但登录页不会成功显示。
- **原因**：如果将网络环境从 office 网络更改为家庭网络，则会出现此问题。 浏览器中有一些缓存。 
- **解决方法**： 
    1.  关闭浏览器，然后重试。 如果问题仍然存在，请继续执行下一步。   
    2.  如果你使用的是 Internet Explorer 浏览器，请尝试以专用模式（按 "Ctrl" + "Shift" + "P"）打开它。 如果使用的是 Chrome 浏览器，请尝试在 incognito 模式下打开它（按 "Ctrl" + "shift" + "N"）。 如果问题仍然存在，请继续执行下一步。 
    3.  尝试使用其他浏览器。 


## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 问题页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)
