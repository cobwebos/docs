---
title: 为 Azure VPN 客户端创建 Intune 配置文件
titleSuffix: Azure VPN Gateway
description: 了解如何创建 Intune 自定义配置文件来部署 Azure VPN 客户端配置文件
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 7105597ec34e804c2f2b85b01feb4824d63005c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578115"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>创建 Intune 配置文件以部署 VPN 客户端配置文件

你可以使用 Microsoft Intune (Windows 10) 部署 Azure VPN 客户端的配置文件。 本文将帮助你使用自定义设置创建 Intune 配置文件。

> [!NOTE]
> 此方法仅适用于部署使用 Azure Active Directory 或公用证书进行客户端身份验证的配置文件。 如果使用了唯一的客户端证书，则每个用户都必须在 Azure VPN 客户端中手动选择正确的证书。
>

## <a name="prerequisites"></a>必备条件

* 设备已注册到 Intune MDM。
* 已在客户端计算机上部署适用于 Windows 10 的 Azure VPN 客户端。
* 仅支持 Windows 版本19H2 或更高版本。

## <a name="modify-xml"></a><a name="xml"></a>修改 XML

在下面的步骤中，我们使用具有以下设置的 Intune 的自定义 OMA-URI 配置文件示例 XML：

* 自动连接
* 受信任的网络检测已启用。

有关其他受支持的选项，请参阅 [VPNV2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) 一文。

1. 从 Azure 门户下载 VPN 配置文件，并从包中提取 *azurevpnconfig.xml* 文件。
1. 将下面的文本复制并粘贴到新的文本编辑器文件中。

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. ```<ServerUrlList>``` ```</ServerUrlList>``` 用下载的配置文件中的条目修改和之间的条目 ( # A0) 。 更改 "TrustedNetworkDetection" FQDN 以适合您的环境。
1. 打开 Azure 下载的配置文件 ( # A0) 并将整个内容复制到剪贴板，方法是突出显示文本并按 (ctrl) + C。 
1. 将上一步中复制的文本粘贴到你在步骤2中创建的标记之间创建的文件 ```<CustomConfiguration>  </CustomConfiguration>``` 。 使用 xml 扩展名保存文件。
1. 记下标记中的值 ```<name>  </name>``` 。 这是配置文件的名称。 在 Intune 中创建配置文件时，将需要此名称。 关闭文件并记住保存该文件的位置。

## <a name="create-intune-profile"></a>创建 Intune 配置文件

在本部分中，将使用自定义设置创建 Microsoft Intune 配置文件。

1. 登录到 Intune 并导航到 " **设备-> 配置文件**"。 选择 " **+ 创建配置文件**"。

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="配置文件&quot;:::
1. 在“平台”中，选择“Windows 10 及更高版本”   。 对于 &quot; **配置文件**&quot;，选择 &quot; **自定义**&quot;。 然后选择“创建”。
1. 为配置文件指定名称和说明，然后选择 &quot; **下一步**&quot;。
1. 在 &quot; **配置设置** &quot; 选项卡上，选择 " **添加**"。

    * **名称：** 输入配置的名称。
    * **说明：** 可选说明。
    * **OMA-URI：** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (可以在<name> </name>标记) 的 azurevpnconfig.xml 文件中找到此信息。
    * **数据类型：** String (XML 文件) 。

   选择文件夹图标，并在 [XML](#xml) 步骤中选择在步骤6中保存的文件。 选择“添加”  。

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="配置文件&quot;:::
1. 在“平台”中，选择“Windows 10 及更高版本”   。 对于 &quot; **配置文件**&quot;，选择 &quot; **自定义**&quot;。 然后选择“创建”。
1. 为配置文件指定名称和说明，然后选择 &quot; **下一步**&quot;。
1. 在 &quot; **配置设置** &quot; 选项卡上，选择 " lightbox="./media/create-profile-intune/configuration-settings.png":::
1. 选择“下一步”。
1. 在 " **分配**" 下，选择要将配置推送到的组。 然后，选择“下一步”****。
1. 适用性规则是可选的。 根据需要定义任何规则，然后选择 " **下一步**"。
1. 在“查看 + 创建”页面上，选择“创建”。 

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="配置文件&quot;:::
1. 在“平台”中，选择“Windows 10 及更高版本”   。 对于 &quot; **配置文件**&quot;，选择 &quot; **自定义**&quot;。 然后选择“创建”。
1. 为配置文件指定名称和说明，然后选择 &quot; **下一步**&quot;。
1. 在 &quot; **配置设置** &quot; 选项卡上，选择 ":::
1. 现在已创建自定义配置文件。 有关部署此配置文件的 Microsoft Intune 步骤，请参阅 [分配用户和设备配置文件](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign)。
 
## <a name="next-steps"></a>后续步骤

有关点到站点的详细信息，请参阅[关于点到站点](point-to-site-about.md)。
