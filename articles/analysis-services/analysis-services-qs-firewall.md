---
title: 快速入门 - 配置 Azure Analysis Services 服务器防火墙 | Microsoft Docs
description: 此快速入门可帮助你使用Azure 门户为 Azure Analysis Services 服务器配置防火墙。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88192433"
---
# <a name="quickstart-configure-server-firewall---portal"></a>快速入门：配置服务器防火墙 - 门户

此快速入门可帮助你为 Azure Analysis Services 服务器配置防火墙。 仅为访问服务器的计算机启用防火墙并配置 IP 地址范围是保护服务器和数据的重要部分。

## <a name="prerequisites"></a>先决条件

- 订阅中的 Analysis Services 服务器。 若要了解详细信息，请参阅[快速入门：创建服务器 - 门户](analysis-services-create-server.md)或[快速入门：创建服务器 - PowerShell](analysis-services-create-powershell.md)
- 客户端计算机的一个或多个 IP 地址范围（如果需要）。

> [!NOTE]
> 在启用了防火墙的情况下，即使“允许来自 Power BI 的访问”设置被设定为“打开”，来自德国 Microsoft 云中 Power BI Premium 的数据导入（刷新）和分页报表连接目前也不受支持。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户 

[登录门户](https://portal.azure.com)

## <a name="configure-a-firewall"></a>配置防火墙

1. 单击服务器打开“概述”页。 
2. 在“设置” > “防火墙” > “启用防火墙”中，选择“打开”   。
3. 若要支持来自 Power BI 和 Power BI Premium 的连接，在“允许来自 Power BI 访问”中，选择“打开” 。  
4. （可选）指定一个或多个 IP 地址范围。 输入每个范围的名称、起始和结束 IP 地址。 防火墙规则名称应限制为 128 个字符，并且只能包含大写字符、小写字符、数字、下划线和连字符。 不允许使用空格和其他特殊字符。
5. 单击“保存” 。

     ![防火墙设置](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除 IP 地址范围或禁用防火墙。

## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何为服务器配置防火墙。 现在你已有服务器，并通过防火墙对其进行了保护，你可以从门户向其添加基本示例数据模型。 拥有一个示例模型有助于了解如何配置模型数据库角色和测试客户端连接。 若要了解更多信息，请继续学习有关添加示例模型的教程。

> [!div class="nextstepaction"]
> [教程：将示例模型添加到服务器](analysis-services-create-sample-model.md)
