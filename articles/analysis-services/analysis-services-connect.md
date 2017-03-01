---
title: "从 Azure Analysis Services 获取数据 |Microsoft Docs"
description: "了解如何连接到 Azure 中的 Analysis Services 服务器并从中获取数据。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: 118ea84abb0de095ac515ee98a643718ca54c043
ms.lasthandoff: 02/16/2017


---
# <a name="get-data-from-azure-analysis-services"></a>从 Azure Analysis Services 获取数据

在 Azure 中创建服务器并向其部署表格模型后，你组织中的用户便可以连接并开始浏览数据。

## <a name="data-providers-aka-client-libraries"></a>数据提供程序（也称为客户端库）

客户端应用程序（例如 Power BI Desktop 和 Microsoft Excel）使用更新的 AMO、ADOMD.NET 和 OLEDB 提供程序连接到 Analysis Services 并与之建立接口通信。 使用某些旧版 Excel 或自定义应用程序时，可能需安装最新版数据提供程序，以便连接到 Azure Analysis Services。 若要了解详细信息，请参阅[数据提供程序](analysis-services-data-providers.md)。

## <a name="server-name"></a>服务器名称

在 Azure 中创建 Analysis Services 服务器时，可以指定唯一名称以及要在其中创建服务器的区域。 在连接中指定服务器名称时，服务器命名方案为：

```
<protocol>://<region>/<servername>
```
 其中，协议是字符串 **asazure**，区域是在其中创建服务器的区域的 URI（例如对于美国西部，则为 westus.asazure.windows.net），且服务器名称是该区域中的唯一服务器名称。

## <a name="get-the-server-name"></a>获取服务器名称

在连接之前，需要获取服务器名称。 在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，然后复制整个服务器名称。 如果组织中的其他用户也在连接此服务器，则可能需要将此服务器名称与他们共享。 指定服务器名称时，必须使用完整路径。

![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>在 Power BI Desktop 中连接

> [!NOTE]
> 此功能为预览版。
> 
> 

1. 在 [Power BI Desktop](https://powerbi.microsoft.com/desktop/) 中，单击“获取数据” > “数据库” > “Azure Analysis Services”。
2. 在“服务器”中，从剪贴板粘贴服务器名称。
3. 在“数据库”中，如果知道要连接到的表格模型数据库或透视的名称，请将其粘贴在此处。 否则，可将此字段留空。 可在下一屏幕上选择数据库或透视。
4. 保留默认选中的“实时连接”选项，然后按“连接”。 如果系统提示输入帐户，请输入你组织的帐户。
5. 在**导航器**中，展开服务器，然后选择要连接到的模型或透视，然后单击“连接”。 单击模型或透视会显示该视图的所有对象。

## <a name="connect-in-power-bi"></a>在 Power BI 中连接

1. 在服务器上创建一个与模型具有实时连接的 Power BI Desktop 文件。
2. 在 [Power BI](https://powerbi.microsoft.com) 中，单击“获取数据” > “文件”。 找到并选择你的文件。

## <a name="connect-in-excel"></a>在 Excel 中连接

可通过使用 Excel 2016 中的“获取数据”或较低版本中的 Power Query 在 Excel 中连接到 Azure Analysis Services。 需要 [MSOLAP.7 提供程序](analysis-services-data-providers.md)。 不支持使用 Power Pivot 中的“导入表向导”进行连接。

> [!NOTE]
> 某些组织在延期频道上部署 Office 365 更新；这意味着从当前版本的版本更新最多将延迟 4 个月。 对于 Excel 2016 内部版本 1609.7369.2115 及更早版本，或 Excel 2013，可创建 Office 数据连接 (.odc) 文件并手动更新 MSOLAP.7 提供程序以连接到 Azure Analysis Services 服务器。 若要了解详细信息，请参阅[创建 .odc 文件](analysis-services-odc.md)。
> 
> 

**从 Excel 2016 进行连接**

1. 在 Excel 2016 的“数据”功能区上，单击“获取外部数据” > “从其他源获取” > “从 Analysis Services 获取”。
2. 在“数据连接向导”的“服务器名称”中，从剪贴板粘贴服务器名称。 然后，在“登录凭据”中，选择“使用以下用户名和密码”，然后键入组织的用户名，例如 nancy@adventureworks.com, 和密码。

    ![“在 Excel 中连接”登录](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. 在“选择数据库和表”中，选择数据库和模型或透视，然后单击“完成”。
   
    ![“在 Excel 中连接”选择模型](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>连接字符串

使用表格对象模型连接到 Azure Analysis Services 时，使用以下连接字符串格式：

###### <a name="integrated-azure-active-directory-authentication"></a>集成的 Azure Active Directory 身份验证

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
集成的身份验证将选取 Azure Active Directory 凭据缓存（如果可用）。 如果不可用，则会显示 Azure 登录窗口。

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>使用用户名和密码进行 Azure Active Directory 身份验证

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="next-steps"></a>后续步骤

[管理服务器](analysis-services-manage.md)


