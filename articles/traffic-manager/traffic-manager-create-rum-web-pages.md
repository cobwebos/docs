---
title: "使用网页将实际用户度量发送到 Azure 流量管理器 | Microsoft Docs"
description: "设置网页将实际用户度量发送到流量管理器"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: c8b345cd973b8cd939ad111132f1afdadae9f2b7
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>如何使用网页将实际用户度量发送到 Azure 流量管理器

>[!NOTE]
>流量管理器中的“实际用户度量”功能以公共预览版形式提供，并且其可用性和可靠性与正式发布版本中的功能可能不在同一级别。 此功能不受支持，可能存在功能限制，并且可能不是在所有 Azure 区域都可用。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure 流量管理器更新](https://azure.microsoft.com/updates/?product=traffic-manager)页。

可以配置网页，通过获取实际用户度量 (RUM) 密钥并将生成的代码嵌入到网页，将实际用户度量发送到流量管理器。

## <a name="obtain-a-real-user-measurements-key"></a>获取实际用户度量密钥

从客户端应用程序获取并发送到流量管理器的度量由使用唯一字符串的服务标识，称为**实际用户度量 (RUM) 密钥**。 可以使用 Azure 门户、REST API 或使用 PowerShell/Azure CLI 获取 RUM 密钥。

若要使用 Azure 门户获取 RUM 密钥，请执行以下操作：
1. 在浏览器中，登录 Azure 门户。 如果还没有帐户，可以注册免费一个月试用版。
2. 在门户的搜索栏中，搜索要修改的流量管理器配置文件名称，并在显示的结果中单击该流量管理器配置文件。
3. 在“流量管理器配置文件”边栏选项卡中，单击“设置”下的“实际用户度量”。
4. 单击“生成密钥”新建 RUM 密钥。
 
  ![生成实际用户度量密钥](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **图 1：生成实际用户度量密钥**

5. 该边栏选项卡现在显示生成的 RUM 密钥和需要嵌入到 HTML 页中的 JavaScript 代码片段。
 
    ![实际用户度量密钥的 Javascript 代码](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **图 2：实际用户度量密钥和度量 JavaScript**
 
6.  单击“复制”按钮复制 JavaScript 代码。 

>[!IMPORTANT]
> 使用生成的 JavaScript 使“实际用户度量”功能正常工作。 对此脚本或“实际用户度量”使用的脚本进行任何更改可能会导致不可预知的行为。

## <a name="embed-the-code-to-an-html-web-page"></a>将代码嵌入到 HTML 网页

获取 RUM 密钥后，下一步是将此复制的 JavaScript 嵌入到最终用户访问的 HTML 页面。 编辑 HTML 可以使用不同的工具和工作流以多种方式完成。 本示例演示如何更新 HTML 页面以添加此脚本。 可以使用本指南使它适合于 HTML 源管理工作流。

1.  在文本编辑器中打开 HTML 页面
2.  将在前面步骤中复制的 JavaScript 代码粘贴到 HTML 的 BODY 部分中（复制的代码位于第 8 行和第 9 行，请参见图 3）。
 
    ![将 Javascript 代码嵌入到实际用户度量网页中](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **图 3：嵌入实际用户度量 JavaScript 的简单 HTML**

3.  保存 HTML 文件并将托管在连接到 Internet 的 Web 服务器上。 
4. 下一次在 Web 浏览器中呈现此页时，将下载引用的 JavaScript，并且脚本将执行度量和报告操作。


## <a name="next-steps"></a>后续步骤
- 详细了解[实际用户度量](traffic-manager-rum-overview.md)
- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

