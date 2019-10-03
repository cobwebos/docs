---
title: 教程 - 为 Azure Active Directory 域服务配置 LDAPS | Microsoft Docs
description: 本教程介绍如何为 Azure Active Directory 域服务托管域配置安全的轻型目录访问协议 (LDAPS)。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 2eaae9093614f1512dcd75d23c98bca871bf2850
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193328"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>教程：为 Azure Active Directory 域服务托管域配置安全 LDAP

若要与 Azure Active Directory 域服务 (Azure AD DS) 托管域通信，需使用轻型目录访问协议 (LDAP)。 默认情况下，LDAP 流量未加密，这对于许多环境而言是一种安全隐患。 借助 Azure AD DS，可将托管域配置为使用安全的轻型目录访问协议 (LDAPS)。 使用安全 LDAP 时，流量将会加密。 安全 LDAP 也称为基于安全套接字层 (SSL)/传输层安全性 (TLS) 的 LDAP。

本教程介绍如何为 Azure AD DS 托管域配置 LDAPS。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建用于 Azure AD DS 的数字证书
> * 为 Azure AD DS 启用安全 LDAP
> * 配置在公共 Internet 上使用的安全 LDAP
> * 绑定和测试 Azure AD DS 托管域的安全 LDAP

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 已计算机上安装 *LDP.exe* 工具。
    * 如果需要，请为 *Active Directory 域服务和 LDAP* 安装[远程服务器管理工具 (RSAT)][rsat]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

本教程使用 Azure 门户为 Azure AD DS 托管域配置安全 LDAP。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-certificate-for-secure-ldap"></a>创建安全 LDAP 的证书

若要使用安全 LDAP，需通过一个数字证书来加密通信。 此数字证书将应用到 Azure AD DS 托管域，可让 *LDP.exe* 等工具在查询数据时使用加密的安全通信。 可通过两种方式创建用于对托管域进行安全 LDAP 访问的证书：

* 来自公共证书颁发机构 (CA) 或企业 CA 的证书。
    * 如果组织从公共 CA 获取证书，请从该公共 CA 获取安全 LDAP 证书。 如果在组织中使用企业 CA，请从企业 CA 获取安全 LDAP 证书。
    * 仅当在 Azure AD DS 托管域中使用自定义 DNS 名称时，公共 CA 才适用。 如果托管域的 DNS 域名以 *.onmicrosoft.com* 结尾，则你无法创建数字证书来保护与此默认域建立的连接。 Microsoft 拥有 *.onmicrosoft.com* 域，因此，公共 CA 不会颁发证书。 在此方案中将创建自签名证书，并使用它来配置安全 LDAP。
* 你自己创建的自签名证书。
    * 此方法适用于测试目的，本教程将予以介绍。

请求或创建的证书必须满足以下要求。 如果你使用无效的证书启用安全 LDAP，托管域将遇到问题：

* **受信任的颁发者** - 证书必须由使用安全 LDAP 连接到托管域的计算机所信任的颁发机构颁发。 此颁发机构可以是公共 CA 或受计算机信任的企业 CA。
* **生存期** - 证书必须至少在接下来的 3 到 6 个月内保持有效。 证书过期后，安全 LDAP 不再可以访问托管域。
* **使用者名称** - 证书上的使用者名称必须是你的托管域。 例如，如果域名为 *contoso.com*，则证书的使用者名称必须是*.contoso.com  。
    * 证书的 DNS 名称或使用者备用名称必须是通配符证书，以确保安全 LDAP 在 Azure AD 域服务中正常工作。 域控制器使用随机名称；可以删除或添加域控制器来确保服务保持可用。
* **密钥用途** - 必须将证书配置用于数字签名和密钥加密。  
* **证书目的** - 证书必须有效，可用于 SSL 服务器身份验证。

本教程使用 PowerShell 为安全 LDAP 创建自签名证书。 以**管理员**身份打开 PowerShell 窗口并运行以下命令。 将 *$dnsName* 变量替换为你自己的托管域使用的 DNS 名称，例如 *contoso.com*：

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

以下示例输出显示证书已成功生成，并存储在本地证书存储 (*LocalMachine\MY*) 中：

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=contoso.com
```

## <a name="understand-and-export-required-certificates"></a>了解和导出所需的证书

若要使用安全 LDAP，需通过公钥基础结构 (PKI) 将网络通信加密。

* **私钥**将应用到 Azure AD DS 托管域。
    * 此私钥用于解密安全 LDAP 通信。  只能将私钥应用到 Azure AD DS 托管域，而不应将其广泛分发到客户端计算机。
    * 包含私钥的证书使用 *.PFX* 文件格式。
* **公钥**将应用到客户端计算机。
    * 此公钥用于加密安全 LDAP 通信。  公钥可分发到客户端计算机。
    * 不包含私钥的证书使用 *.CER* 文件格式。

这两个密钥（私钥和公钥）确保只有适当的计算机才能成功地相互通信。   如果你使用公共 CA 或企业 CA，系统将为你颁发包含私钥的证书，该证书可应用到 Azure AD DS 托管域。 客户端计算机应已知道并信任公钥。 在本教程中，你已创建一个包含私钥的自签名证书，因此，需要导出相应的私钥和公钥部分。

### <a name="export-a-certificate-for-azure-ad-ds"></a>导出 Azure AD DS 的证书

在 Azure AD DS 托管域上使用上一步骤中创建的数字证书之前，请先将该证书导出到包含私钥的 *.PFX* 证书文件。

1. 若要打开“运行”对话框，请按 **Windows** + **R** 键。 
1. 在“运行”对话框中输入 **mmc** 并选择“确定”，打开 Microsoft 管理控制台 (MMC)。  
1. 在“用户帐户控制”提示窗口中单击“是”，以管理员身份启动 MMC。  
1. 在“文件”菜单中，单击“添加/删除管理单元...”。  
1. 在“证书管理单元”向导中，依次选择“计算机帐户”、“下一步”。   
1. 在“选择计算机”页上，依次选择“本地计算机: (运行此控制台的计算机)”、“完成”。   
1. 在“添加或删除管理单元”对话框中，单击“确定”将证书管理单元添加到 MMC。  
1. 在 MMC 窗口中，展开“控制台根节点”。  选择“证书(本地计算机)”，然后依次展开“个人”节点和“证书”节点。   

    ![在 Microsoft 管理控制台中打开个人证书存储](./media/tutorial-configure-ldaps/open-personal-store.png)

1. 此时会显示上一步骤中创建的自签名证书，例如 *contoso.com*。 右键单击此证书，然后选择“所有任务”>“导出...”。 

    ![在 Microsoft 管理控制台中导出证书](./media/tutorial-configure-ldaps/export-cert.png)

1. 在“证书导出向导”中，选择“下一步”。  
1. 必须导出证书的私钥。 如果导出的证书不包含私钥，为托管域启用安全 LDAP 的操作将会失败。

    在“导出私钥”页上，依次选择“是，导出私钥”、“下一步”。   
1. Azure AD DS 托管域仅支持包含私钥的 *.PFX* 证书文件格式。 不要将证书导出为不包含私钥的 *.CER* 证书文件格式。

    在“导出文件格式”页上，选择“个人信息交换 - PKCS #12 (.PFX)”作为导出证书的文件格式。   选中“包括证书路径中的所有证书(如果可能)”框： 

    ![选择以 PKCS 12 (.PFX) 文件格式导出证书的选项](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. 由于此证书用于解密数据，因此应小心控制访问权限。 可以通过一个密码来保护证书的使用。 如果未设置正确的密码，则该证书不可应用到服务。

    在“安全性”页上，选择“密码”对应的选项来保护 *.PFX* 证书文件。   输入并确认密码，然后选择“下一步”。  下一部分将使用此密码来为 Azure AD DS 托管域启用安全 LDAP。
1. 在“要导出的文件”页上，指定要将证书导出到的文件名和位置，例如 *C:\Users\accountname\azure-ad-ds.pfx*。 
1. 在复查页上，选择“完成”以将证书导出到 *.PFX* 证书文件。  成功导出证书后，会显示确认对话框。
1. 请将 MMC 保持打开状态，以便在下一部分使用。

### <a name="export-a-certificate-for-client-computers"></a>为客户端计算机导出证书

客户端计算机必须信任安全 LDAP 证书的颁发者，才能成功使用 LDAPS 连接到托管域。 客户端计算机需要使用一个证书才能成功加密 Azure AD DS 解密的数据。 如果你使用公共 CA，计算机应会自动信任这些证书颁发者，并获得相应的证书。 本教程使用自签名证书，并生成一个包含上一步骤中创建的私钥的证书。 现在，让我们导出该自签名证书，然后将其安装到客户端计算机上的受信任证书存储中。

1. 返回到“证书(本地计算机)”>“个人”>“证书”存储的 MMC。  此时会显示前一步骤中创建的自签名证书，例如 *contoso.com*。 右键单击此证书，然后选择“所有任务”>“导出...”。 
1. 在“证书导出向导”中，选择“下一步”。  
1. 由于不需要对客户端使用私钥，因此请在“导出私钥”页上，依次选择“否，不导出私钥”、“下一步”。   
1. 在“导出文件格式”页上，选择“Base-64 编码 X.509 (.CER)”作为导出证书的文件格式：  

    ![选择以 Base-64 编码 X.509 (.CER) 文件格式导出证书的选项](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. 在“要导出的文件”页上，指定要将证书导出到的文件名和位置，例如 *C:\Users\accountname\azure-ad-ds-client.cer*。 
1. 在复查页上，选择“完成”以将证书导出到 *.CER* 证书文件。  成功导出证书后，会显示确认对话框。

现在，可将 *.CER* 证书文件分发到需要信任与 Azure AD DS 托管域建立的安全 LDAP 连接的客户端计算机。 让我们在本地计算机上安装证书。

1. 打开文件资源管理器并浏览到 *.CER* 证书文件的保存位置，例如 *C:\Users\accountname\azure-ad-ds-client.cer*。
1. 右键单击该 *.CER* 证书文件，然后选择“安装证书”。 
1. 在“证书导入向导”中，选择将证书存储在“本地计算机”中，然后选择“下一步”：   

    ![选择将证书导入到本地计算机存储的选项](./media/tutorial-configure-ldaps/import-cer-file.png)

1. 出现提示时，请选择“是”以允许计算机进行更改。 
1. 依次选择“基于证书类型自动选择证书存储”、“下一步”。  
1. 在复查页上，选择“完成”以导入 *.CER* 证书。  成功导入证书后，会显示确认对话框。

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>为 Azure AD DS 启用安全 LDAP

创建并导出包含私钥的数字证书，并将客户端计算机设置为信任连接后，接下来请在 Azure AD DS 托管域上启用安全 LDAP。 若要在 Azure AD DS 托管域上启用安全 LDAP，请执行以下配置步骤：

1. 在 [Azure 门户](https://portal.azure.com)上的“搜索资源”框中搜索“域服务”。   从搜索结果中“选择 Azure AD 域服务”  。

    ![在 Azure 门户中搜索并选择 Azure AD DS 托管域](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. 选择你的托管域，例如 *contoso.com*。
1. 在 Azure AD DS 窗口的左侧，选择“安全 LDAP”。 
1. 默认情况下，已禁用对托管域的安全 LDAP 访问。 将“安全 LDAP”切换为“启用”   。
1. 默认情况下，已禁用通过 Internet 对托管域的安全 LDAP 访问。 启用公共安全 LDAP 访问后，域很容易受到来自 Internet 的密码暴力破解攻击。 下一步骤将配置一个网络安全组，以仅限所需的源 IP 地址范围进行访问。

    将“允许通过 Internet 进行安全 LDAP 访问”切换为“启用”   。

1. 选择“包含安全 LDAP 证书的 .PFX 文件”旁边的文件夹图标。  浏览到 *.PFX* 文件的路径，然后选择上一步骤中创建的包含私钥的证书。

    如前面的证书要求部分中所述，不能在默认的 *.onmicrosoft.com* 域中使用来自公共 CA 的证书。 Microsoft 拥有 *.onmicrosoft.com* 域，因此，公共 CA 不会颁发证书。 请确保证书采用适当的格式。 否则，在启用安全 LDAP 时，Azure 平台会生成证书验证错误。

1. 输入在上一步骤中将证书导出到 *.PFX* 文件时设置的**用于解密 .PFX 文件的密码**。
1. 选择“保存”以启用安全 LDAP。 

    ![在 Azure 门户中为 Azure AD DS 托管域启用安全 LDAP](./media/tutorial-configure-ldaps/enable-ldaps.png)

此时会显示一条通知，指出正在为托管域配置安全 LDAP。 在完成此操作之前，无法修改托管域的其他设置。

为托管域启用安全 LDAP 需要花费几分钟时间。 如果提供的安全 LDAP 证书不符合所需的条件，为托管域启用安全 LDAP 的操作将会失败。 失败的一些常见原因包括域名不正确、证书即将过期或已过期。 可以使用有效的参数重新创建证书，然后使用此更新的证书启用安全 LDAP。

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>锁定通过 Internet 进行的安全 LDAP 访问

启用通过 Internet 对 Azure AD DS 托管域进行安全 LDAP 访问时，会对安全造成威胁。 可在 Internet 上通过 TCP 端口 636 访问托管域。 建议仅限环境中的特定已知 IP 地址访问托管域。 可以使用 Azure 网络安全组规则来限制对安全 LDAP 的访问。

让我们创建一个规则，以允许从指定的一组 IP 地址通过 TCP 端口 636 进行入站安全 LDAP 访问。 低优先级的默认“全部拒绝”规则将应用到来自 Internet 的所有其他入站流量，因此只有指定的地址才能使用安全 LDAP 访问 Azure AD DS 托管域。 

1. 在 Azure 门户上的左侧导航栏中选择“资源组”。 
1. 选择资源组（例如 *myResourceGroup*），然后选择网络安全组（例如 *AADDS-contoso.com-NSG*）。
1. 此时会显示现有的入站和出站安全规则列表。 在网络安全组窗口的左侧，选择“安全性”>“入站安全规则”。 
1. 选择“添加”，然后创建一个允许 *TCP* 端口 *636* 的规则。  为提高安全性，请选择“IP 地址”作为源，然后为组织指定自己的有效 IP 地址或范围。 

    | 设置                           | 值        |
    |-----------------------------------|--------------|
    | 源                            | IP 地址 |
    | 源 IP 地址/CIDR 范围 | 环境的有效 IP 地址或范围 |
    | 源端口范围                | *            |
    | 目标                       | 任意          |
    | 目标端口范围           | 636          |
    | 协议                          | TCP          |
    | 操作                            | 允许        |
    | 优先度                          | 401          |
    | Name                              | AllowLDAPS   |

1. 准备就绪后，选择“添加”以保存并应用该规则。 

    ![创建用于保护通过 Internet 进行的 LDAPS 访问的网络安全组规则](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>为外部访问配置 DNS 区域

启用通过 Internet 的安全 LDAP 访问后，请更新 DNS 区域，使客户端计算机能够找到此托管域。 “安全 LDAP 外部 IP 地址”列在 Azure AD DS 托管域的“属性”选项卡上：  

![在 Azure 门户中查看 Azure AD DS 托管域的安全 LDAP 外部 IP 地址](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

配置外部 DNS 提供程序，以创建一条可解析为此外部 IP 地址的主机记录（例如 *ldaps*）。 若要先在计算机本地进行测试，可在 Windows hosts 文件中创建一个条目。 若要成功编辑本地计算机上的 hosts 文件，请以管理员身份打开“记事本”，然后打开文件 *C:\Windows\System32\drivers\etc*。 

外部 DNS 提供程序或本地 hosts 文件中的以下示例 DNS 条目将 *ldaps.contoso.com* 的流量解析为外部 IP 地址 *40.121.19.239*：

```
40.121.19.239    ldaps.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>测试对托管域的查询

若要连接并绑定到 Azure AD DS 托管域并通过 LDAP 进行搜索，也可以使用 *LDP.exe*。 此工具已包含在远程服务器管理工具 (RSAT) 包中。 有关详细信息，请参阅[安装远程服务器管理工具][rsat]。

1. 打开 *LDP.exe* 并连接到托管域。 依次选择“连接”、“连接...”。  
1. 输入在上一步骤中为托管域创建的安全 LDAP DNS 域名，例如 *ldaps.contoso.com*。 若要使用安全 LDAP，请将“端口”设置为 *636*，然后选中“SSL”框。  
1. 选择“确定”连接到托管域。 

接下来，绑定到 Azure AD DS 托管域。 如果你在 Azure AD DS 实例上禁用了 NTLM 密码哈希同步，则用户（和服务帐户）将无法执行 LDAP 简单绑定。 有关禁用 NTLM 密码哈希同步的详细信息，请参阅[保护 Azure AD DS 托管域][secure-domain]。

1. 选择“连接”菜单选项，然后选择“绑定...”。  
1. 提供属于“AAD DC 管理员”组的用户帐户的凭据，例如 *contosoadmin*。  输入用户帐户的密码，然后输入域，例如 *contoso.com*。
1. 对于“绑定类型”，请选择“使用凭据绑定”选项。  
1. 选择“确定”绑定到 Azure AD DS 托管域。 

若要查看 Azure AD DS 托管域中存储的对象：

1. 选择“视图”菜单选项，然后选择“树”。  
1. 将“基础 DN”字段保留空白，然后选择“确定”。  
1. 选择一个容器（例如“AADDC 用户”），然后右键单击该容器并选择“搜索”。  
1. 保留预先填充的字段设置，然后选择“运行”。  查询结果将显示在右侧窗口中。

    ![使用 LDP.exe 搜索 Azure AD DS 托管域中的对象](./media/tutorial-configure-ldaps/ldp-query.png)

若要直接查询特定的容器，可以通过“视图”>“树”菜单指定一个“基础 DN”，例如“OU=AADDC Users,DC=CONTOSO,DC=COM”或“OU=AADDC Computers,DC=CONTOSO,DC=COM”。     有关如何格式化和创建查询的详细信息，请参阅 [LDAP 查询基础知识][ldap-query-basics]。

## <a name="clean-up-resources"></a>清理资源

如果你在本教程中将一个 DNS 条目添加到了计算机的本地 hosts 文件以测试连接，请删除此条目，并在 DNS 区域中添加一条正式的记录。 若要从本地 hosts 文件中删除该条目，请完成以下步骤：

1. 在本地计算机上，以管理员身份打开“记事本” 
1. 浏览到并打开文件 *C:\Windows\System32\drivers\etc*
1. 删除所添加的记录对应的行，例如 `40.121.19.239    ldaps.contoso.com`

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建用于 Azure AD DS 的数字证书
> * 为 Azure AD DS 启用安全 LDAP
> * 配置在公共 Internet 上使用的安全 LDAP
> * 绑定和测试 Azure AD DS 托管域的安全 LDAP

> [!div class="nextstepaction"]
> [为混合 Azure AD 环境配置密码哈希同步](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
