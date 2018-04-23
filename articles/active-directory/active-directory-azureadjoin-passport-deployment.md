---
title: 在组织中启用 Microsoft Windows Hello 企业版 | Microsoft 文档
description: 在组织中启用 Microsoft Passport 的部署说明。
services: active-directory
documentationcenter: ''
keywords: 配置 Microsoft Passport、Microsoft Windows Hello for Business 部署
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>在组织中启用 Microsoft Windows Hello for Business
在[将已加入域的 Windows 10 设备与 Azure Active Directory 相连](active-directory-azureadjoin-devices-group-policy.md)后，执行以下操作在组织中启用 Microsoft Windows Hello for Business：

1. 部署 System Center Configuration Manager  
2. 配置策略设置
3. 配置证书配置文件  

## <a name="deploy-system-center-configuration-manager"></a>部署 System Center Configuration Manager
若要基于 Windows Hello for Business 密钥部署用户证书，需要满足以下条件：

* **System Center Configuration Manager 当前分支** - 需要安装版本 1606 或更高版本。 有关详细信息，请参阅 [System Center Configuration Manager 相关文档](https://technet.microsoft.com/library/mt346023.aspx)和 [System Center Configuration Manager 团队博客](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。
* **公钥基础结构 (PKI)** - 若要通过使用用户证书启用 Microsoft Windows Hello for Business，必须部署 PKI。 如果没有 PKI，或不希望将它用于用户证书，可以部署一个已安装 Windows Server 2016 版本 10551（或更高版本）的全新域控制器。 按照步骤操作，[在现有域中安装副本域控制器](https://technet.microsoft.com/library/jj574134.aspx)或[安装新的 Active Directory 林（如果正在创建一个新环境）](https://technet.microsoft.com/library/jj574166)。 （可从 [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true) 下载 ISO。）

## <a name="configure-policy-settings"></a>配置策略设置
若要配置 Microsoft Windows Hello for Business 策略设置，可以使用两个选项：

* Active Directory 中的组策略 
* System Center Configuration Manager 

若要配置 Microsoft Windows Hello for Business 策略设置，推荐方法是使用 Active Directory 中的组策略。 

如果还要用它部署证书，首选方法是使用 System Center Configuration Manager。 在此方案中：

* 确保兼容较新的部署方案
* 在客户端 Windows 10 版本 1607 或更高版本上，同样有此要求。

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>通过 Active Directory 中的组策略配置 Microsoft Windows Hello for Business
**步骤**：

1. 打开“服务器管理器”，并导航到“工具” > “组策略管理”。
2. 从“组策略管理”，导航到与要启用“Azure AD Join”功能的域相对应的域节点。
3. 右键单击“组策略对象”并选择“新建”。 为组策略对象命名，例如“启用 Windows Hello for Business”。 单击“确定”。
4. 右键单击新建的组策略对象，并选择“编辑”。
5. 导航到“计算机配置” > “策略” > “管理模板” > “Windows  组件” > “Windows Hello for Business”。
6. 右键单击“启用 Windows Hello for Business”，并选择“编辑”。
7. 选择“已启用”选项按钮，并单击“应用”。 单击“确定”。
8. 现在可以将该组策略对象链接到所选位置。 要为组织中所有已加入域的 Windows 10 设备启用此策略，请将此组策略链接到相应域。 例如：
   * 已加入域的 Windows 10 计算机将位于的 Active Directory 中的特定组织单位 (OU)
   * 包含会自动注册到 Azure AD 的已加入域的 Windows 10 计算机的特定安全组

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>使用 System Center Configuration Manager 配置 Windows Hello for Business
**步骤**：

1. 打开“System Center Configuration Manager”，并导航到“资源和合规性”>“合规性设置”>“公司资源访问”>“Windows Hello for Business 配置文件”。
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. 在顶部工具栏中，单击“创建 Windows Hello for Business 配置文件”。
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. 在“常规”对话框中，执行以下步骤：
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. 在“名称”文本框中，键入配置文件的名称，例如“My WHfB Profile”。
   
    b. 单击“资源组名称” 的 Azure 数据工厂。
4. 在“支持的平台”对话框中，选择将使用该 Windows Hello for Business 配置文件进行配置的平台，然后单击“下一步”。
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. 在“设置”对话框中，执行以下步骤：
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. 显示“配置 Windows Hello for Business”时，选择“已启用”。
   
    b. 显示“使用受信任的平台模块 (TPM)”时，选择“必需”。 
   
    c. 显示“身份验证方法”时，选择“基于证书”。
   
    d. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“摘要”对话框中，单击“下一步”。
7. 在“完成”对话框中，单击“关闭”。
8. 在顶部工具栏中，单击“部署”。
   
    ![配置 Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>配置证书配置文件
如果针对本地身份验证使用的是基于证书的身份验证，需要配置并部署证书配置文件。 此任务要求你在 System Center Configuration Manager 中设置 NDES 服务器和“证书注册点”站点角色。 有关详细信息，请参阅 [Configuration Manager 中证书配置文件的先决条件](https://technet.microsoft.com/library/dn261205.aspx)。

1. 打开“System Center Configuration Manager”，并导航到“资源和合规性”>“合规性设置”>“公司资源访问”>“证书配置文件”。
2. 选择具有智能卡登录扩展密钥用法 (EKU) 的模板。

在证书配置文件的“SCEP 注册”页上，需要选择“安装到 Passport for Work，否则失败”作为“密钥存储提供程序”。

## <a name="next-steps"></a>后续步骤
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [无密码身份验证](active-directory-azureadjoin-passport.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时会已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)

