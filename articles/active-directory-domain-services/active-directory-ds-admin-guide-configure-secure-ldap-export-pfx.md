---
title: 在 Azure AD 域服务中配置安全 LDAP (LDAPS) | Microsoft 文档
description: 为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 178302bd28e83da73590faf2be98fe5d2853ce56
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586879"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)

## <a name="before-you-begin"></a>开始之前
请确保已完成[任务 1：获取安全 LDAP 的证书](active-directory-ds-admin-guide-configure-secure-ldap.md)。


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>任务 2 - 将安全 LDAP 证书导出到 .PFX 文件
在开始此任务之前，请确保已从公共证书颁发机构获取安全 LDAP 证书，或已创建自签名证书。

执行以下步骤，将 LDAPS 证书导出到 .PFX 文件。

1. 按“开始”按钮并键入 **R**。在“运行”对话框中键入 **mmc**，并单击“确定”。

    ![启动 MMC 控制台](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. 在“用户帐户控制”提示窗口中单击“是”，以管理员身份启动 MMC（Microsoft 管理控制台）。
3. 在“文件”菜单中，单击“添加/删除管理单元...”。

    ![将管理单元添加到 MMC 控制台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. 在“添加或删除管理单元”对话框中，选择“证书”管理单元，并单击“添加 >”按钮。

    ![将证书管理单元添加到 MMC 控制台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. 在“证书管理单元”向导中，选择“计算机帐户”并单击“下一步”。

    ![为计算机帐户添加证书管理单元](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. 在“选择计算机”页上，选择“本地计算机:(运行此控制台的计算机)”并单击“完成”。

    ![添加证书管理单元 - 选择计算机](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. 在“添加或删除管理单元”对话框中，单击“确定”将证书管理单元添加到 MMC。

    ![将证书管理单元添加到 MMC - 完成](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. 在 MMC 窗口中，单击“控制台根节点”将它展开。 应会看到“证书”管理单元已加载。 单击“证书(本地计算机)”将它展开。 依次单击“个人”节点和“证书”节点将其展开。

    ![打开个人证书存储](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. 此时应会显示所创建的自签名证书。 可以检查证书的属性，确保指纹与创建证书时 PowerShell 窗口中报告的指纹相匹配。
10. 选择自签名证书并**单击右键**。 在右键单击菜单中，依次选择“所有任务”和“导出...”。

    ![导出证书](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. 在“证书导出向导”中，单击“下一步”。

    ![导出证书向导](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. 在“导出私钥”页上，选择“是，导出私钥”，并单击“下一步”。

    ![导出证书私钥](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > 必须连同证书一起导出私钥。 如果提供的 PFX 不包含证书的私钥，则为托管域启用安全 LDAP 会失败。
    >
    >
13. 在“导出文件格式”页上，选择“个人信息交换 - PKCS #12 (.PFX)”作为导出证书的文件格式。

    ![导出证书文件格式](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > 仅支持 .PFX 文件格式。 请不要将证书导出为 .CER 文件格式。
    >
    >
14. 在“安全”页上选择“密码”选项，并输入用于保护 .PFX 文件的密码。 请记住此密码，因为下一个任务要用到它。 单击“下一步”继续。

    ![证书导出密码 ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > 记下此密码。 在[任务 3 - 为托管域启用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) 中为此托管域启用安全 LDAP 时，需要用到此密码
    >
    >
15. 在“要导出的文件”页上，指定文件名以及证书要导出到的位置。

    ![证书导出路径](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. 在下一页上单击“完成”，将证书导出到 PFX 文件。 证书导出后，应会显示确认对话框。

    ![证书导出完成](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>后续步骤
[任务 3 - 为托管域启用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
