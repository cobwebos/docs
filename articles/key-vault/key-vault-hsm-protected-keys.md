---
title: "如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥 | Microsoft Docs"
description: "使用这篇文章可帮助你规划、生成然后传输你自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。 也称为 BYOK 或自带密钥。"
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: ambapat
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: f481de9d7eb23531b4be82c3b61e4f3850bed72e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥
## <a name="introduction"></a>介绍
为了提高可靠性，你在使用 Azure 密钥保管库时，可以在硬件安全模块 (HSM) 中导入或生成永不离开 HSM 边界的密钥。 这种情况通常被称为*自带密钥*，简称 BYOK。 这些 HSM 都通过 FIPS 140-2 第 2 级验证。 Azure 密钥保管库使用 HSM 的 Thales nShield 系列来保护你的密钥。

使用本主题中的信息，可帮助你规划、生成然后传输你自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。

此功能不适用于 Azure China。

> [!NOTE]
> 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](key-vault-whatis.md)  
>
> 如需包括为受 HSM 保护的密钥创建密钥保管库的入门教程，请参阅 [Azure 密钥保管库入门](key-vault-get-started.md)。
>
>

有关生成和通过 Internet 传输受 HSM 保护的密钥的详细信息：

* 从脱机工作站生成密钥，这样可以减小攻击面。
* 密钥通过密钥交换密钥 (KEK) 进行加密，且在传输到 Azure 密钥保管库 HSM 之前一直处于加密状态。 只有加密版本的密钥会离开原始工作站。
* 该工具集会在将你的密钥绑定到 Azure 密钥保管库安全体系的租户密钥上设置属性。 因此，在 Azure 密钥保管库 HSM 收到和解密你的密钥之后，只有这些 HSM 能够使用它。 无法导出你的密钥。 此绑定由 Thales HSM 强制实施。
* 用于加密你的密钥的密钥交换密钥 (KEK) 是在 Azure 密钥保管库 HSM 内部生成的，且不可导出。 HSM 会强制使 HSM 外部没有清晰版本的 KEK。 此外，该工具集还包括来自 Thales 的证明，即 KEK 不可导出，并在 Thales 制造的正版 HSM 内部生成。
* 该工具集包括来自 Thales 的证明，即 Azure 密钥保管库安全体系也在 Thales 制造的正版 HSM 内部生成。 这可向你证明 Microsoft 使用的是正版硬件。
* Microsoft 会在每个地理区域都使用单独的 KEK 和单独的安全体系。 这种分离可确保你的密钥只能在将其加密的区域的数据中心使用。 例如，来自欧洲客户的密钥不能在北美或亚洲数据中心使用。

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Thales HSM 和 Microsoft 服务的详细信息
Thales E-security 是全球领先的数据加密和网络安全解决方案供应商，主要面向金融服务、高科技、制造、政府和技术行业。 Thales E-security 在保护企业和政府信息方面拥有 40 年的骄人历史记录，目前规模最大的五大能源和航天公司中有四家都在使用 Thales 解决方案。 此外，还有 22 个北约国家/地区也使用了他们的解决方案，全球有超过 80% 支付交易都受其保护。

Microsoft 已与 Thales 联手增强 HSM 的技术开发水平。 这些增强功能可使您能够获得托管服务的典型优势，而且无需放弃对密钥的控制权。 具体来说，这些增强功能可以让 Microsoft 管理 HSM，这样你就不必费心管理了。 作为云服务，Azure 密钥保管库无需通知即可扩大，以满足组织的使用高峰需求。 同时，密钥也会在 Microsoft 的 HSM 内部获得保护：因为你生成密钥并将其传输到 Microsoft 的 HSM，所以你可以保留对密钥生命周期的控制权。

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>为 Azure 密钥保管库实现“自带密钥 (BYOK)”
如果将生成自己的受 HSM 保护的密钥，然后将其传输到 Azure 密钥保管库（“自带密钥 (BYOK)”方案），请使用以下信息和过程。

## <a name="prerequisites-for-byok"></a>BYOK 的先决条件
有关适用于 Azure 密钥保管库的“自带密钥 (BYOK)”的先决条件列表，请参阅下表。

| 要求 | 详细信息 |
| --- | --- |
| Azure 订阅 |若要创建 Azure 密钥保管库，需要 Azure 订阅：[注册免费试用版](https://azure.microsoft.com/pricing/free-trial/) |
| 用于支持受 HSM 保护的密钥的 Azure 密钥保管库高级服务层 |请参阅 [Azure 密钥保管库定价](https://azure.microsoft.com/pricing/details/key-vault/)网站，了解有关 Azure 密钥保管库的服务层和功能的详细信息。 |
| Thales HSM、智能卡和支持软件 |必须具有 Thales 硬件安全模块的访问权限和 Thales HSM 的基本操作知识。 请参阅 [Thales 硬件安全模块](https://www.thales-esecurity.com/msrms/buy)，了解兼容型号的列表，或者如果你还没有 HSM，请购买 HSM。 |
| 以下硬件和软件：<ol><li>脱机 x64 工作站，最低 Windows 操作系统为 Windows 7，Thales nShield 软件最低为 11.50 版。<br/><br/>如果此工作站运行 Windows 7，则必须[安装 Microsoft.NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)。</li><li>连接到 Internet 的工作站，最低 Windows 操作系统为 Windows 7，最低 [Azure PowerShell](/powershell/azure/overview) 安装版本为 1.1.0。</li><li>至少拥有 16 MB 可用空间的 USB 驱动器或其他便携式存储设备。</li></ol> |出于安全原因，建议第一个工作站不要连接到网络。 但是，此建议不会以编程方式强制执行。<br/><br/>请注意，在后面的说明中，将此工作站称为连接断开的工作站。</p></blockquote><br/>此外，如果租户密钥用于生产网络，我们建议你使用第二个独立的工作站来下载工具集和上载租户密钥。 但出于测试目的，可以使用与第一个相同的工作站。<br/><br/>请注意，在后面的说明中，将第二个工作站称为连接到 Internet 的工作站。</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>生成密钥并将其传输到 Azure 密钥保管库 HSM
将使用以下五个步骤来生成密钥并将其传输到 Azure 密钥保管库 HSM：

* [步骤 1：准备连接到 Internet 的工作站](#step-1-prepare-your-internet-connected-workstation)
* [步骤 2：准备连接断开的工作站](#step-2-prepare-your-disconnected-workstation)
* [步骤 3：生成密钥](#step-3-generate-your-key)
* [步骤 4：准备要传输的密钥](#step-4-prepare-your-key-for-transfer)
* [步骤 5：将密钥传输到 Azure 密钥保管库](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>步骤 1：准备连接到 Internet 的工作站
在步骤 1 中，请对连接到 Internet 的工作站执行以下过程。

### <a name="step-11-install-azure-powershell"></a>步骤 1.1：安装 Azure PowerShell
从通过 Internet 连接的工作站，下载并安装Azure PowerShell 模块，其包含用于管理 Azure 密钥保管库的 cmdlet。 这要求模块的最低版本为 0.8.13。

如需安装说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a name="step-12-get-your-azure-subscription-id"></a>步骤 1.2：获取 Azure 订阅 ID
使用以下命令启动 Azure PowerShell 会话，并登录你的 Azure 帐户：

        Add-AzureAccount
在弹出的浏览器窗口中，输入你的 Azure 帐户用户名和密码。 然后，使用 [Get-azuresubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) 命令：

        Get-AzureSubscription
从输出中，找到将用于 Azure 密钥保管库的订阅的 ID。 稍后你将会用到该订阅 ID。

不要关闭 Azure PowerShell 窗口。

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>步骤 1.3：下载 Azure 密钥保管库的 BYOK 工具集
转到 Microsoft 下载中心，针对你所在的地理区域或 Azure 实例[下载 Azure 密钥保管库的 BYOK 工具集](http://www.microsoft.com/download/details.aspx?id=45345)。 使用以下信息确定要下载的包名称及其对应的 SHA 256 包哈希：

- - -
**美国：**

KeyVault-BYOK-Tools-UnitedStates.zip

760EE9BD6445C87CFF0E8B032577118704B3BEAA045AA55977C10EF68BC67E2B

- - -
**欧洲：**

KeyVault-BYOK-Tools-Europe.zip

7A64B94225F59B847C5C27C2200BAD7D16C901E1687767EDBBB8B09BB285011D

- - -
**亚洲：**

KeyVault-BYOK-Tools-AsiaPacific.zip

813DC94B23079CF7A5CEA71D5B444E86B292F463C53EE47AED25D4F7CD58E7D8

- - -
**拉丁美洲：**

KeyVault-BYOK-Tools-LatinAmerica.zip

3F29069E3500F95C0E156F4B8914E1DC60C20FB64B464306A299EA5145D755C0

- - -
**日本：**

KeyVault-BYOK-Tools-Japan.zip

453FFEA2F8F410720B68B8BAC4CF79135A7F37F4E491FF840BE9E69E88A98C90

- - -
韩国：

KeyVault-BYOK-Tools-Korea.zip

C17B7E93224DA80F5668E09CF7DAE2F92527E8226179995BBE2E43DA4323595A

- - -
**澳大利亚：**

KeyVault-BYOK-Tools-Australia.zip

4AD893396E86F2D2A71682876A6A8EA59E3C7895BEAD2F7E7C8516682582C34B

- - -
[**Azure Government：**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

3AAE1A96B9D15B899B8126CFC0380719EB54FDF2EA94489B43FAD21ECC745F64

- - -
美国政府国防部：

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A61E78297B0732DF2682FDE63D7B572CE4D23B0BC27CC48AFF620BD060BB9E9D

- - -
**加拿大：**

KeyVault-BYOK-Tools-Canada.zip

30B87A0BA8208F6B7241C30C794FED1C370D7445ACA179685816E4E156CD2AF7

- - -
**德国：**

KeyVault-BYOK-Tools-Germany.zip

5E3E4AA54715E4F93C3C145035B18275B7C6815A06D7ABB212E7FADBF2929261

- - -
**印度：**

KeyVault-BYOK-Tools-India.zip

136733A6C6A71D75571BB80819B3D55A9B83CCAD5C996C686BC5682A3F369BF7

- - -
**英国：**

KeyVault-BYOK-Tools-UnitedKingdom.zip

ED331A6F1D34A402317D3F27D5396046AF0E5C2D44B5D10CCCE293472942D268

- - -

若要验证已下载的 BYOK 工具集的完整性，请从 Azure PowerShell 会话中使用 [Get-filehash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet。

    Get-FileHash KeyVault-BYOK-Tools-*.zip

该工具集包括以下内容：

* 名称以 **BYOK-KEK-pkg-** 开头的密钥交换密钥 (KEK) 包。
* 名称以 **BYOK-SecurityWorld-pkg-** 开头的的安全体系包。
* 名称为 verifykeypackage.py 的 Python 脚本。
* 名称为 **KeyTransferRemote.exe** 的命令行可执行文件以及关联的 DLL。
* 名称为 **vcredist_x64.exe** 的 Visual c + + 可再发行组件包。

将该软件包复制到 USB 驱动器或其他便携式存储设备。

## <a name="step-2-prepare-your-disconnected-workstation"></a>步骤 2：准备连接断开的工作站
在步骤 2 中，请对未连接到网络（Internet 或内部网络）的工作站执行以下过程。

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>步骤 2.1：准备使用 Thales HSM 的连接断开的工作站
在 Windows 计算机上安装 nCipher (Thales) 支持软件，然后将 Thales HSM 连接到该计算机。

确保 Thales 工具位于路径 (%nfast_home%\bin) 下。 例如，请键入以下内容：

        set PATH=%PATH%;"%nfast_home%\bin"

有关详细信息，请参阅 Thales HSM 附带的用户指南。

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>步骤 2.2：在连接断开的工作站上安装 BYOK 工具集
从 USB 驱动器或其他便携式存储设备中复制 BYOK 工具集包，然后执行以下操作：

1. 将下载的程序包中的文件解压到任意文件夹中。
2. 从该文件夹中运行 vcredist_x64.exe。
3. 按照说明安装 Visual Studio 2013 的 Visual c + + 运行时组件。

## <a name="step-3-generate-your-key"></a>步骤 3：生成密钥
在步骤 3 中，请对连接断开的工作站执行以下过程。 若要完成此步骤，HSM 必须处于初始化模式。 


### <a name="step-31-change-the-hsm-mode-to-i"></a>步骤 3.1：将 HSM 模式更改为“I”
如果使用 Thales nShield Edge，要更改模式，请执行以下操作：1. 使用“模式”按钮突出显示所需模式。 2. 几秒钟内，按住“清除”按钮几秒钟。 如果模式发生更改，新模式的 LED 指示灯将停止闪烁并保持亮起。 状态 LED 可能会不规则地闪烁几秒钟，然后在设备就绪时规则闪烁。 否则，设备保持在当前模式，相应的模式 LED 亮起。

### <a name="step-32-create-a-security-world"></a>步骤 3.2：创建安全体系
启动命令提示符并运行 Thales new-world 程序。

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

此程序会在 %NFAST_KMDATA%\local\world 中创建一个 **Security World** 文件，此文件夹对应于 C:\ProgramData\nCipher\Key Management Data\local 文件夹。 可以使用不同的值进行仲裁，但在本例中，系统会提示你为每个值输入三个空白卡和 pin。 然后，任何两个卡都会提供安全体系的完全访问权限。 这些卡成为新安全体系的**管理员卡集**。

然后执行以下操作：

* 备份体系文件。 保障和保护体系文件、管理员卡及其 pin，并确保无人可拥有多个卡的访问权限。

### <a name="step-33-change-the-hsm-mode-to-o"></a>步骤 3.3：将 HSM 模式更改为“O”
如果使用 Thales nShield Edge，要更改模式，请执行以下操作：1. 使用“模式”按钮突出显示所需模式。 2. 几秒钟内，按住“清除”按钮几秒钟。 如果模式发生更改，新模式的 LED 指示灯将停止闪烁并保持亮起。 状态 LED 可能会不规则地闪烁几秒钟，然后在设备就绪时规则闪烁。 否则，设备保持在当前模式，相应的模式 LED 亮起。


### <a name="step-34-validate-the-downloaded-package"></a>步骤 3.4：验证下载的包
此步骤是可选的，但建议你执行，以便能够验证以下内容：

* 工具集附带的密钥交换密钥已从正版 Thales HSM 中生成。
* 工具集附带的安全体系哈希已在正版 Thales HSM 中生成。
* 密钥交换密钥不可导出。

> [!NOTE]
> 若要验证下载的程序包，HSM 必须处于连接状态且接通电源，并且在其上必须包含一个安全体系（例如你刚刚创建了一个）。
>
>

验证下载的程序包：

1. 根据你所在的地理区域或 Azure 实例，键入下列其中一个区域，以运行 verifykeypackage.py 脚本：

   * 北美洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * 欧洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * 亚洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * 拉丁美洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * 日本：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * 韩国：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * 澳大利亚：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * 对于 [Azure Government](https://azure.microsoft.com/features/gov/)，会使用美国政府的 Azure 实例：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * 美国政府国防部：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * 加拿大：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * 德国：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * 印度：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > Thales 软件包含在 %NFAST_HOME%\python\bin 中的 python
     >
     >
2. 确认你看到以下消息，表示验证成功：**Result: SUCCESS**

此脚本会验证直到 Thales 根密钥的签名程序链。 此根密钥的哈希嵌入到脚本中，其值应为 **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**。 你还可以通过访问 [Thales 网站](http://www.thalesesec.com/)单独确认此值。

现在已准备好新建密钥。

### <a name="step-35-create-a-new-key"></a>步骤 3.5：新建密钥
通过使用 Thales **generatekey** 程序生成一个密钥。

运行以下命令来生成密钥：

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

运行此命令时，请使用以下说明︰

* 参数 *protect* 必须设置为值 **module**，如上所示。 这将创建一个受模块保护的密钥。 BYOK 工具集不支持受 OCS 保护的密钥。
* 将 **ident** 和 **plainname** 的 *contosokey* 值替换为任何字符串值。 为了最大程度减少管理开销并降低错误风险，建议对这两个参数使用相同的值。 **ident** 值只能包含数字、短划线和小写字母。
* 在此示例中，pubexp 留空（默认值），但你可以指定特定值。 有关详细信息，请参阅 Thales 文档。

此命令在 %NFAST_KMDATA%\local 文件夹中创建一个标记化密钥文件，文件名以 **key_simple_** 开头，后跟在命令中指定的 **ident**。 例如：**key_simple_contosokey**。 此文件包含已加密的密钥。

在安全位置备份这个标记化密钥文件。

> [!IMPORTANT]
> 稍后你将自己的密钥传送到 Azure 密钥保管库时，Microsoft 就无法将此密钥导出返回给你，因此，请务必安全地备份密钥和安全体系。 请联系 Thales，获取有关备份密钥的指导和最佳实践。
>
>

现在已准备好将密钥传输到 Azure 密钥保管库。

## <a name="step-4-prepare-your-key-for-transfer"></a>步骤 4：准备要传输的密钥
在步骤 4 中，请对断开连接的工作站执行以下过程。

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>步骤 4.1：使用减少权限创建密钥的副本

打开新命令提示符并将当前目录更改为解压缩 BYOK zip 文件的位置。 若要减少密钥的权限，请从命令提示符处运行以下命令，具体要取决于你所在的地理区域或 Azure 实例：

* 北美洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* 欧洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* 亚洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* 拉丁美洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* 日本：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* 韩国：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* 澳大利亚：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* 对于 [Azure Government](https://azure.microsoft.com/features/gov/)，会使用美国政府的 Azure 实例：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* 美国政府国防部：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* 加拿大：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* 德国：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* 印度：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

运行此命令时，请将 contosokey 替换为在[生成密钥](#step-3-generate-your-key)步骤的“步骤 3.5：新建密钥”中指定的相同值。

系统会要求你插入安全体系的管理员卡。

此命令完成后，你会看到 **Result: SUCCESS**，同时权限减少的密钥副本位于名为 key_xferacId_<contosokey> 的文件中。

可使用 Thales 实用程序通过以下命令检查 ACLS：

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  运行这些命令时，将 contosokey 替换为在[生成密钥](#step-3-generate-your-key)步骤的“步骤 3.5：新建密钥”中指定的相同值。

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>步骤 4.2：使用 Microsoft 的密钥交换密钥加密密钥
运行以下一项命令，具体要取决于你所在的地理区域或 Azure 实例：

* 北美洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 欧洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 亚洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 拉丁美洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 日本：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 韩国：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 澳大利亚：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 对于 [Azure Government](https://azure.microsoft.com/features/gov/)，会使用美国政府的 Azure 实例：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 美国政府国防部：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 加拿大：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 德国：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 印度：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

运行此命令时，请使用以下说明︰

* 将 contosokey 替换为用于在[生成密钥](#step-3-generate-your-key)步骤的“步骤 3.5：新建密钥”中生成密钥的标识符。
* 将 *SubscriptionID* 替换为包含密钥保管库的 Azure 订阅 ID。 以前已在[准备连接到 Internet 的工作站](#step-1-prepare-your-internet-connected-workstation)步骤的**步骤 1.2：获取 Azure 订阅 ID** 中检索此值。
* 将 *ContosoFirstHSMKey* 替换为用于输出文件名称的标签。

此操作成功完成后，将会显示“结果: 成功”，并且包含 KeyTransferPackage-ContosoFirstHSMkey.byok 的当前文件夹中会出现一个新文件

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>步骤 4.3：将密钥传输包复制到连接 Internet 的工作站
使用 USB 驱动器或其他便携式存储设备，将上一步的输出文件 (KeyTransferPackage-ContosoFirstHSMkey.byok) 复制到连接 Internet 的工作站。

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>步骤 5：将密钥传输到 Azure 密钥保管库
在最后一步中，在连接 Internet 的工作站上，使用 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet 上传你从断开连接的工作站中复制到 Azure 密钥保管库 HSM 的密钥传输包：

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

如果上载成功，你会看到刚添加的密钥的属性。

## <a name="next-steps"></a>后续步骤
现在可以在密钥保管库中使用此受 HSM 保护的密钥。 有关详细信息，请参阅 [Azure 密钥保管库入门](key-vault-get-started.md)中的**如果你要使用硬件安全模块 (HSM)**部分。

