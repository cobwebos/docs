---
title: "如何打开应用程序代理应用程序所需的防火墙端口 | Microsoft Docs"
description: "了解要使 Azure AD 应用程序代理正常工作需要打开哪些端口"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8ecd6d7e666d362194126a4abba7a65f2c7b8b6b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>如何打开应用程序代理应用程序所需的防火墙端口

若要查看所需端口的完整列表及每个端口的功能，请参阅[应用程序代理文档](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)的先决条件部分。 请注意，应用程序代理仅使用出站端口。

还可以通过从本地网络打开[连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)，检查是否已打开所需的全部端口。 绿色复选标记越多表示复原能力越强。 

## <a name="app-proxy-regions"></a>应用代理区域

我们会提供一种方法，告知哪些区域对你来说需要为绿色。 目前，请确保这些区域全部为绿色。 不管处在哪个区域，美国中部都不可或缺。

为了确保工具提供正确的结果，请务必：

-   在安装有连接器的服务器的浏览器上打开该工具。

-   确保适用于连接器的任何代理或防火墙也应用到此页。 可以在 Internet Explorer 上通过转到“设置” -&gt;“Internet 选择” -&gt;“连接” -&gt;“LAN 设置”完成此操作。 在此页上，会看到“为 LAN 使用代理服务器”字段。 选中此框，将代理地址输入“地址”字段。

## <a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)
