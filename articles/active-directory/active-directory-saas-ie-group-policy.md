---
title: "使用 GPO 为 IE 部署 Azure 访问面板扩展 | Microsoft 文档"
description: "如何使用组策略为我的应用门户部署 Internet Explorer 加载项。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 53ce95811bbeb306ae986fda91d3387db4e71998
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>如何使用组策略部署 Internet Explorer 的访问面板扩展
本教程说明如何使用组策略在用户的计算机上远程安装 Internet Explorer 的访问面板扩展。 需要登录到使用[基于密码的单一登录](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)配置的应用程序的 Internet Explorer 用户必须使用此扩展。

建议管理员将此扩展的部署自动化。 否则，用户必须自行下载并安装扩展，这样很容易出现用户错误，并且需要管理员权限。 本教程介绍使用组策略自动化软件部署的一种方法。 [了解有关组策略的详细信息。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

访问面板扩展也适用于 [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) 和 [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998)，两者都不需要管理员权限即可安装。

## <a name="prerequisites"></a>先决条件
* 已设置 [Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，并且已将用户的计算机加入你的域。
* 必须拥有“编辑设置”权限才能编辑组策略对象 (GPO)。 默认情况下，以下安全组的成员拥有此权限：域管理员、企业管理员和组策略创建者和所有者。 [了解详细信息。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>步骤 1：创建分发点
首先，必须将安装程序包放在网络位置中，该位置可以由要在其上远程安装扩展的计算机访问。 为此，请执行以下步骤：

1. 以管理员身份登录到服务器
2. 在“服务器管理器”窗口中，转到“文件和存储服务”。
   
    ![打开文件和存储服务](./media/active-directory-saas-ie-group-policy/files-services.png)
3. 转到“共享”选项卡。 然后单击“任务” > “新建共享...”
   
    ![打开文件和存储服务](./media/active-directory-saas-ie-group-policy/shares.png)
4. 完成“新建共享向导”并设置权限，确保可以从用户的计算机访问该共享位置。 [了解有关共享的详细信息。](https://technet.microsoft.com/library/cc753175.aspx)
5. 下载以下 Microsoft Windows 安装程序包（.msi 文件）：[Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. 将该安装程序包复制到共享中所需的位置。
   
    ![将 .msi 文件复制到共享。](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. 验证客户端计算机是否可从共享访问该安装程序包。 

## <a name="step-2-create-the-group-policy-object"></a>步骤 2：创建组策略对象
1. 登录托管所安装的 Active Directory 域服务 (AD DS) 的服务器。
2. 在“服务器管理器”中，转到“工具” > “组策略管理”。
   
    ![转到“工具”>“组策略管理”](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. 在“组策略管理”窗口的左窗格中，查看组织单位 (OU) 层次结构并确定想要应用组策略的范围。 例如，可以选择一个小型 OU 并将其部署到少量用户以进行测试，或者选择一个顶级 OU 并将其部署到整个组织。
   
   > [!NOTE]
   > 如果想要创建或编辑组织单位 (OU)，请切换回到“服务器管理器”，然后转到“工具” > “Active Directory 用户和计算机”。
   > 
   > 
4. 选择 OU 后，请右键单击它，然后选择“在这个域中创建 GPO 并在此处链接它...”
   
    ![创建新 GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. 在“新建 GPO”提示窗口中，输入新组策略对象的名称。
   
    ![为新 GPO 命名](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. 右键单击创建的组策略对象，然后选择“编辑”。
   
    ![编辑新 GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>步骤 3：分配安装包
1. 确定是要根据“计算机配置”还是“用户配置”部署扩展。 如果使用[计算机配置](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)，则无论哪个用户登录到计算机，都会在该计算机上安装扩展。 如果使用[用户配置](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)，则无论用户登录到哪一台计算机，系统都会为该用户安装扩展。
2. 在“组策略管理编辑器”窗口的左窗格中，根据选择的配置类型，转到以下文件夹路径之一：
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. 右键单击“软件安装”，然后选择“新建” > “包...”
   
    ![创建新的软件安装包](./media/active-directory-saas-ie-group-policy/new-package.png)
4. 转到包含[步骤 1：创建分发点](#step-1-create-the-distribution-point)中所述安装程序包的共享文件夹，选择 .msi 文件，然后单击“打开”。
   
   > [!IMPORTANT]
   > 如果该共享也位于此服务器上，请验证是否可以通过网络文件路径（而不是本地文件路径）访问此 .msi。
   > 
   > 
   
    ![从共享文件夹中选择安装包。](./media/active-directory-saas-ie-group-policy/select-package.png)
5. 在“部署软件”提示窗口中，选择“已分配”作为部署方法。 然后单击“确定”。
   
    ![选择“已分配”，然后单击“确定”。](./media/active-directory-saas-ie-group-policy/deployment-method.png)

扩展现已部署到选定的 OU。 [了解有关组策略软件安装的详细信息。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>步骤 4：自动启用 Internet Explorer 的扩展
除了运行安装程序以外，还必须显式启用 Internet Explorer 的每个扩展，只有这样才能使用该扩展。 请执行以下步骤，使用组策略启用访问面板扩展：

1. 根据在[步骤 3：分配安装包](#step-3-assign-the-installation-package)中选择的配置类型，在“组策略管理编辑器”窗口中转到以下路径之一：
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. 右键单击“加载项列表”，然后选择“编辑”。
    ![编辑加载项列表。](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. 在“加载项列表”窗口中，选择“已启用”。 然后，在“选项”部分中单击“显示...”。
   
    ![单击“启用”，然后单击“显示...”](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. 在“显示内容”窗口中执行以下步骤：
   
   1. 对于第一列（“值名称”字段），请复制并粘贴以下类 ID：`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. 对于第二列（“值”字段），请输入以下值：`1`
   3. 单击“确定”关闭“显示内容”窗口。
      
      ![如上所示填写值。](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. 单击“确定”应用更改并关闭“加载项列表”窗口。

现在，应该为选定 OU 中的计算机启用了该扩展。 [详细了解如何使用组策略启用或禁用 Internet Explorer 加载项。](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>步骤 5（可选）：禁用“记住密码”提示
当用户登录到使用访问面板扩展的网站时，Internet Explorer 可能显示以下提示，询问“是否存储密码?”

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

如果不希望用户看到此提示，请根据以下步骤防止自动完成功能记住密码：

1. 在“组策略管理编辑器”窗口中，转到下面列出的路径。 此配置设置仅在“用户配置”下提供。
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. 查找名为“对表单上的用户名和密码打开自动完成功能”的设置。
   
   > [!NOTE]
   > 旧版的 Active Directory 可能列出此设置，但其名称为“不允许自动完成功能保存密码”。 该设置的配置不同于本教程中所述的设置。
   > 
   > 
   
    ![请记得在“用户设置”下查找此设置。](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. 右键单击上述设置，然后选择“编辑”。
4. 在标题为“对表单上的用户名和密码打开自动完成功能”的窗口中选择“禁用”。
   
    ![选择“禁用”](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. 单击“确定”应用这些更改并关闭窗口。

用户将不再能存储其凭据或使用自动完成功能来访问以前存储的凭据。 但是，此策略允许用户对其他类型的表单字段（例如搜索字段）继续使用自动完成功能。

> [!WARNING]
> 如果在用户选择存储某些凭据之后才启用此策略，此策略*不会*清除已存储的凭据。
> 
> 

## <a name="step-6-testing-the-deployment"></a>步骤 6：测试部署
遵循以下步骤验证是否已成功部署扩展：

1. 如果扩展是使用“计算机配置”部署的，请登录到属于[步骤 2：创建组策略对象](#step-2-create-the-group-policy-object)中选择的 OU 的客户端计算机。 如果扩展是使用“用户配置”部署的，请务必使用属于该 OU 的用户的身份登录。
2. 可能要登录好几次才能在此计算机上完全更新组策略更改。 若要强制更新，请打开“命令提示”窗口，然后运行以下命令：`gpupdate /force`
3. 必须重新启动计算机才能开始安装。 安装扩展时，启动花费的时间可能比平时要长很多。
4. 重新启动后，打开 **Internet Explorer**。 在窗口右上角单击“工具”（齿轮图标），然后选择“管理加载项”。
   
    ![转到“工具”>“管理加载项”](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. 在“管理加载项”窗口中，检查“访问面板扩展”是否已安装且其“状态”设置为“已启用”。
   
    ![检查访问面板扩展是否已安装并启用。](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [Azure Active Directory 的应用程序访问与单一登录](active-directory-appssoaccess-whatis.md)
* [Internet Explorer 访问面板扩展故障排除](active-directory-saas-ie-troubleshooting.md)


