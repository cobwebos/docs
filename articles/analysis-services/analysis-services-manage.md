---
title: 管理 Azure Analysis Services | Microsoft Docs
description: 了解如何在 Azure 中管理 Analysis Services 服务器。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a589a75c1d8c353c7e8dabc508904282e28cf371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597682"
---
# <a name="manage-analysis-services"></a>管理 Analysis Services
在 Azure 中创建 Analysis Services 服务器后，可能会有一些需要立即执行或在稍后操作期间执行的管理任务。 例如，处理刷新数据、控制谁有权访问服务器上的模型，或者监视服务器的运行状况。 某些管理任务仅可在 Azure 门户中执行，而其他一些可在 SQL Server Management Studio (SSMS) 中执行，还有部分在两者中均可执行。

## <a name="azure-portal"></a>Azure 门户
在 [Azure 门户](http://portal.azure.com/)中，可创建和删除服务器、监控服务器资源、更改大小和管理有权访问服务器的人员。  如果遇到问题，可提交支持请求。

![在 Azure 中获取服务器名称](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
就像连接到自身组织中的服务器实例一样，在 Azure 中连接到服务器。 可在 SSMS 中执行许多相同的任务，例如处理数据或创建处理脚本、管理角色和使用 PowerShell。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>下载并安装 SSMS
若要获取全部最新功能，并在连接至 Azure Analysis Services 服务器时享受最流畅的体验，请确保使用最新版本的 SSMS。 

[下载 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


### <a name="to-connect-with-ssms"></a>连接 SSMS
 使用 SSMS 时，在首次连接到服务器之前，需确保用户名已包含在 Analysis Services 管理员组中。 有关详细信息，请参阅本文后面的[服务器管理员](#server-administrators)。

1. 在连接之前，需要获取服务器名称。 在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，并复制服务器名称。
   
    ![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。
3. 在“连接到服务器”对话框中，粘贴服务器名称，然后在“身份验证”中选择以下身份验证类型之一：   
    > [!NOTE]
    > 建议选择“Active Directory - 通用且具有 MFA 支持”身份验证类型。

    > [!NOTE]
    > 如果使用 Microsoft 帐户、Live ID、Yanoo、Gmail 等登录，请将密码字段留空。 单击“连接”后，系统将提示你输入密码。

    选择“Windows 身份验证”，以使用 Windows 域\用户名和密码凭据。

    **Active Directory 密码身份验证**，其使用组织帐户。 例如，从未加入域的计算机进行连接时。

    选择“Active Directory - 通用且具有 MFA 支持”，以使用[非交互式或多重身份验证](../sql-database/sql-database-ssms-mfa-authentication.md)。 
   
    ![在 SSMS 中连接](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>服务器管理员和数据库用户
Azure Analysis Services 中存在两种类型的用户，即服务器管理员和数据库用户。 这两种类型的用户必须存在于 Azure Active Directory 中，且必须由组织电子邮件地址或 UPN 指定。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。


## <a name="troubleshooting-connection-problems"></a>连接问题疑难解答
若在使用 SSMS 进行连接时遇到问题，则可能需要清除登录缓存。 光盘中不缓存任何内容。若要清除缓存，请关闭并重新启动连接进程。 

## <a name="next-steps"></a>后续步骤
如果尚未将表格模型部署到新服务器，现在正是一个好时机。 有关详细信息，请参阅[部署到 Azure Analysis Services](analysis-services-deploy.md)。

如果已将模型部署到服务器，可使用客户端或浏览器连接到该模型。 有关详细信息，请参阅[从 Azure Analysis Services 获取数据](analysis-services-connect.md)。

