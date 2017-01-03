---
title: "Azure Active Directory 中的条件性访问入门 - 预览版 | Microsoft 文档"
description: "使用位置条件测试条件性访问。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f4f159c832865bc2ea0c24a8a072cade3bca8e33
ms.openlocfilehash: 327238372d84efdb7f2d58867a366821534fc945


---
# <a name="get-started-with-conditional-access-in-azure-active-directory---preview"></a>Azure Active Directory 中的条件性访问入门 - 预览版

本主题中所述的行为目前以[预览版](active-directory-preview-explainer.md)提供。

条件性访问是 Azure Active Directory 的一项功能，用于定义在哪种条件下允许已授权的用户访问应用。 

本主题提供有关如何根据环境中的位置条件测试条件性访问的说明。  


## <a name="scenario-description"></a>方案描述

许多组织中的一个常见要求是，只有从企业 Intranet 外部访问应用时，才需要执行多重身份验证。 使用 Azure Active Directory，可以通过配置基于位置的条件性访问策略来轻松实现此目标。 本主题详细说明如何配置相关策略。 策略利用[受信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) 来区分从企业 Intranet 尝试的访问与从所有其他位置进行的访问。


## <a name="prerequisites"></a>先决条件

本主题中所述的方案假设读者熟悉 [Azure Active Directory 条件性访问](active-directory-conditional-access-azure-portal.md)中所述的概念。

若要测试此方案，需要：

- 创建一个测试用户 

- 向该测试用户分配 Azure AD Premium 许可证

- 配置一个托管的应用并向其分配测试用户

- 配置受信任的 IP

如需有关受信任 IP 的详细信息，请参阅[受信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。


## <a name="policy-configuration-steps"></a>策略配置步骤

**若要配置条件性访问策略，请执行以下操作：**

1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory”。 

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. 上“Azure Active Directory”边栏选项卡的“管理”部分中，单击“条件性访问”。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. 在“条件性访问”边栏选项卡顶部的工具栏中单击“添加”，打开“新建”边栏选项卡。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. 在“新建”边栏选项卡中的“名称”文本框内，键入策略的名称。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. 在“分配”部分中，单击“用户和组”。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. 在“用户和组”边栏选项卡中执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“选择用户和组”。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“选择”。

    c. 在“选择”边栏选项卡中选择测试用户，然后单击“选择”。

    d.单击“下一步”。 在“用户和组”边栏选项卡中，单击“完成”。

7. 在“新建”边栏选项卡的“分配”部分中单击“云应用”，打开“云应用”边栏选项卡。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. 在“云应用”边栏选项卡中执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“选择应用”。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“选择”。

    c. 在“选择”边栏选项卡中选择你的云应用，然后单击“选择”。

    d.单击“下一步”。 在“云应用”边栏选项卡中，单击“完成”。

9. 在“新建”边栏选项卡的“分配”部分中单击“条件”，打开“条件”边栏选项卡。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. 在“条件”边栏选项卡中单击“位置”，打开“位置”边栏选项卡。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. 在“位置”边栏选项卡中执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“配置”下面，单击“是”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“包括”下面，单击“所有位置”。

    c. 单击“排除”，然后单击“所有受信任的 IP”。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d.单击“下一步”。 单击“Done”（完成） 。

12. 在“条件”边栏选项卡中，单击“完成”。

13. 在“新建”边栏选项卡的“控制”部分中单击“授权”，打开“授权”边栏选项卡。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. 在“授权”边栏选项卡中执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“需要多重身份验证”。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“选择”。

15. 在“新建”边栏选项卡中的“启用策略”下面，单击“打开”。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. 在“新建”边栏选项卡中，单击“创建”。


## <a name="testing-the-policy"></a>测试策略

若要测试策略，应该从符合以下条件的设备访问你的应用： 

1. 使用已配置的“受信任的 IP”中的某个 IP 地址 

1. 使用不属于已配置的“受信任的 IP”的某个 IP 地址

应该是只有在通过不使用已配置的受信任 IP 的设备尝试建立连接时，才需要执行多重身份验证。 


## <a name="next-steps"></a>后续步骤

若要了解有关条件性访问的详细信息，请参阅 [Azure Active Directory 条件性访问](active-directory-conditional-access-azure-portal.md)。




<!--HONumber=Dec16_HO5-->


