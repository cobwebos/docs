---
title: 排查 Azure 数据工厂中“复制数据”工具的问题
description: 了解如何排查 Azure 数据工厂中“复制数据”工具的问题。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388348"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>排查 Azure 数据工厂中“复制数据”工具的问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨排查 Azure 数据工厂中“复制数据”工具的问题的常用方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>错误代码：无法在“复制数据”工具中验证

- **症状**：在“复制数据”工具的第一步中，遇到了“无法验证”的警告消息。
- 原因：禁用所有第三方 Cookie 时可能会发生这种情况。
- **解决方法**： 
    - 使用 Internet Explorer 或 Microsoft Edge 浏览器。
    - 如果使用的是 Chrome 浏览器，请按照以下说明为 microsoftonline.com 和 windows.net 添加 Cookie 例外 。
        1.  打开 Chrome 浏览器。
        2.  单击右侧的扳手或三条线（自定义并控制 Google Chrome）。
        3.  单击“设置”。
        4.  在“高级设置”下搜索 Cookie 或转到“隐私” 。
        5.  选择“内容设置”。    
        6.  Cookie 应设置为“允许设置本地数据(推荐)”。
        7.  单击“管理例外”。 在“主机名模式”下输入以下内容，并确保已设置为“允许” 。
            - login.microsoftonline.com
            - login.windows.net
        8.  关闭浏览器并重新启动。
    - 如果使用的是 Firefox 浏览器，请按照以下说明添加 Cookie 例外。
        1. 从 Firefox 菜单中转到“工具” > “选项” 。
        2. 在“隐私” > “历史记录”下，可能会看到当前设置为“对历史记录使用自定义设置”  。
        3. 在“接受第三方 Cookie”中，当前设置可能为“从不”，则应在右侧单击“例外”以添加以下站点  。
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  关闭浏览器并重新启动。 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>错误代码：无法打开登录页并输入密码

- **症状**：“复制数据”工具会将你重定向到登录页，但登录页不会成功显示。
- 原因：如果将网络环境从办公室网络更改为家庭网络，则可能会发生此问题。 浏览器中有一些缓存。 
- **解决方法**： 
    1.  请关闭浏览器并重试。 如果问题仍然存在，请继续执行下一步。   
    2.  如果使用的是 Internet Explorer 浏览器，请尝试以隐私模式打开它（按 Ctrl+Shift+P）。 如果使用的是 Chrome 浏览器，请尝试以无痕模式打开它（按 Ctrl+Shift+N）。 如果问题仍然存在，请继续执行下一步。 
    3.  尝试使用其他浏览器。 


## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 问题页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)
