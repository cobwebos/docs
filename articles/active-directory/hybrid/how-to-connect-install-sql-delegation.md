---
title: 使用 SQL 委派的管理员权限安装 Azure AD Connect | Microsoft Docs
description: 本主题介绍如何通过 Azure AD Connect 更新使用只有 SQL dbo 权限的帐户进行安装。
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60243560"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>使用 SQL 委派的管理员权限安装 Azure AD Connect
在最新的 Azure AD Connect 版本之前，不支持在部署需要 SQL 的配置时使用管理委派。  若要安装 Azure AD Connect，用户需具有 SQL Server 的服务器管理员 (SA) 权限。

现在，在使用 Azure AD Connect 最新版本的情况下，可以由 SQL 管理员在带外进行数据库预配，然后由具有数据库所有者权限的 Azure AD Connect 管理员完成安装。

## <a name="before-you-begin"></a>开始之前
在使用此功能之前需认识到，存在多个移动部件，每一个可能涉及到组织中的不同管理员。  下表汇总了各个角色及其在使用此功能部署 Azure AD Connect 的过程中的相应任务。

|角色|描述|
|-----|-----|
|域或林 AD 管理员|创建域级服务帐户，供 Azure AD Connect 用于运行同步服务。  有关服务帐户的详细信息，请参阅[帐户和权限](reference-connect-accounts-permissions.md)。
|SQL 管理员|创建 ADSync 数据库，授予登录 + dbo 访问权限给 Azure AD Connect 管理员以及域/林管理员创建的服务帐户。|
Azure AD Connect 管理员|安装 Azure AD Connect 并在自定义安装过程中指定服务帐户。

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>使用 SQL 委派的权限安装 Azure AD Connect 的步骤
若要使用数据库所有者权限预配带外数据库并安装 Azure AD Connect，请使用以下步骤。

>[!NOTE]
>**强烈建议**在创建数据库时选择 Latin1_General_CI_AS 排序规则，虽然这不是必需的。


1. 让 SQL 管理员使用不区分大小写的排序规则序列 **(Latin1_General_CI_AS)** 创建 ADSync 数据库。  数据库必须命名为 **ADSync**。  安装 Azure AD Connect 时，会将恢复模型、兼容性级别和包含类型更新为正确的值。  但是，必须由 SQL 管理员来正确设置排序规则序列，否则 Azure AD Connect 会阻止该安装。  若要进行恢复，SA 必须删除并重新创建数据库。
 
   ![Collation](./media/how-to-connect-install-sql-delegation/sql4.png)
2. 向 Azure AD Connect 管理员和域服务帐户授予以下权限：
   - SQL 登录名 
   - **数据库所有者 (dbo)** 权限。
 
   ![权限](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect 不支持使用嵌套成员身份登录。  这意味着你的 Azure AD Connect 管理员帐户和域服务帐户必须关联到一个被授予了 dbo 权限的登录名。  它不能仅仅是被分配给某个登录名（具有 dbo 权限）的组的成员。

3. 向 Azure AD Connect 管理员发送一封电子邮件，指出在安装 Azure AD Connect 时应使用的 SQL Server 和实例。

## <a name="additional-information"></a>其他信息
预配数据库以后，Azure AD Connect 管理员可以在方便的情况下安装并配置本地同步。

如果 SQL 管理员从以前的 Azure AD Connect 备份还原了 ADSync 数据库，你需要使用现有的数据库安装新的 Azure AD Connect 服务器。 若要详细了解如何通过现有数据库来安装 Azure AD Connect，请参阅[使用现有 ADSync 数据库安装 Azure AD Connect](how-to-connect-install-existing-database.md)。

## <a name="next-steps"></a>后续步骤
- [通过快速设置开始使用 Azure AD Connect](how-to-connect-install-express.md)
- [Azure AD Connect 的自定义安装](how-to-connect-install-custom.md)
- [使用现有 ADSync 数据库安装 Azure AD Connect](how-to-connect-install-existing-database.md)  
