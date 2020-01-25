---
title: 使用模板将 Windows VM 加入到 Azure AD DS |Microsoft Docs
description: 了解如何使用 Azure 资源管理器模板将新的或现有的 Windows Server VM 加入 Azure Active Directory 域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: 7bf01eea71134d932305cce7665c68d4dcc655cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712568"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>使用资源管理器模板将 Windows Server 虚拟机加入到 Azure Active Directory 域服务托管域

若要自动执行 Azure 虚拟机（Vm）的部署和配置，可以使用资源管理器模板。 这些模板使你可以每次创建一致的部署。 还可以将扩展包含在模板中，以在部署过程中自动配置 VM。 一个有用的扩展将 Vm 加入到域中，该域可与 Azure Active Directory 域服务（Azure AD DS）托管域一起使用。

本文说明如何使用资源管理器模板创建 Windows Server VM 并将其加入到 Azure AD DS 托管域。 你还将了解如何将现有 Windows Server VM 加入到 Azure AD DS 域。

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="azure-resource-manager-template-overview"></a>Azure 资源管理器模板概述

资源管理器模板使你可以在代码中定义 Azure 基础结构。 所有必需的资源、网络连接或 Vm 的配置都可以在模板中进行定义。 这些模板每次都创建一致的可重现部署，并且可以在进行更改时进行版本控制。 有关详细信息，请参阅[Azure 资源管理器模板概述][template-overview]。

每个资源都是使用 JavaScript 对象表示法（JSON）在模板中定义的。 下面的 JSON 示例使用*virtualMachines/extensions*资源类型来安装 Active Directory 域加入扩展。 使用在部署时指定的参数。 部署扩展后，VM 将加入指定 Azure AD DS 托管域。

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

即使不在同一个模板中创建 VM，也可以部署此 VM 扩展。 本文中的示例演示了以下两种方法：

* [创建 Windows Server VM 并将其加入托管域](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [将现有 Windows Server VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>创建 Windows Server VM 并将其加入托管域

如果需要 Windows Server VM，可以使用资源管理器模板创建和配置一个。 部署 VM 时，会安装扩展，以将 VM 加入到 Azure AD DS 托管域。 如果已有要加入到 Azure AD DS 托管域的 VM，请跳到将[现有 Windows SERVER VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)。

若要创建 Windows Server VM，请将其加入到 Azure AD DS 托管域中，完成以下步骤：

1. 浏览到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。 选择要**部署到 Azure**的选项。
1. 在 "**自定义部署**" 页上，输入以下信息以创建 WINDOWS Server VM 并将其加入到 Azure AD DS 托管域：

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | 리소스 그룹            | 选择 VM 的资源组。 |
    | 위치                  | 选择 VM 的位置。 |
    | 现有 VNET 名称        | 要将 VM 连接到的现有虚拟网络的名称，例如*myVnet*。 |
    | 现有子网名称      | 现有虚拟网络子网的名称，如*工作负荷*。 |
    | DNS 标签前缀          | 输入要用于 VM 的 DNS 名称，例如*myvm*。 |
    | VM 크기                   | 指定 VM 大小，如*Standard_DS2_v2*。 |
    | 要加入的域            | Azure AD DS 托管域 DNS 名称，例如*aadds.contoso.com*。 |
    | 도메인 사용자 이름           | 应用于将 VM 加入托管域的 Azure AD DS 托管域中的用户帐户，如 `contosoadmin@aadds.contoso.com`。 此帐户必须是 " *AZURE AD DC 管理员*" 组的成员。 |
    | 域密码           | 在上一个设置中指定的用户帐户的密码。 |
    | 可选 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果未指定此参数的值，则会将 VM 添加到默认的*AAD DC 计算机*OU。 |
    | VM 管理员用户名         | 指定要在虚拟机上创建的本地管理员帐户。 |
    | VM 管理员密码         | 为 VM 指定本地管理员密码。 创建一个强的本地管理员密码以防止密码暴力攻击。 |

1. 查看条款和条件，并选中 "**我同意上述条款和条件**" 复选框。 准备就绪后，选择 "**购买**" 创建 VM，并将其加入到 Azure AD DS 托管域。

> [!WARNING]
> **암호 처리에 주의하세요.**
> 模板参数文件请求作为*AZURE AD DC administrators*组成员的用户帐户的密码。 不要手动向此文件中输入值，而是将其保留在文件共享或其他共享位置。

成功完成部署需要几分钟时间。 完成后，会创建 Windows VM，并将其加入到 Azure AD DS 托管域。 可以管理 VM，也可以使用域帐户登录。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>관리되는 도메인에 기존 Windows Server VM 조인

如果你有要加入到 Azure AD DS 托管域的现有 VM 或 Vm 组，则可以使用资源管理器模板来仅部署 VM 扩展。

若要将现有 Windows Server VM 加入到 Azure AD DS 托管域中，请完成以下步骤：

1. 浏览到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。 选择要**部署到 Azure**的选项。
1. 在 "**自定义部署**" 页上，输入以下信息以将 VM 加入到 Azure AD DS 托管域：

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | 리소스 그룹            | 选择包含现有 VM 的资源组。 |
    | 위치                  | 选择现有 VM 的位置。 |
    | VM 列表                   | 输入要加入到 Azure AD DS 托管域的现有 VM 的逗号分隔列表，例如*myVM1、myVM2*。 |
    | 域加入用户名     | 应用于将 VM 加入托管域的 Azure AD DS 托管域中的用户帐户，如 `contosoadmin@aadds.contoso.com`。 此帐户必须是 " *AZURE AD DC 管理员*" 组的成员。 |
    | 域加入用户密码 | 在上一个设置中指定的用户帐户的密码。 |
    | 可选 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果未指定此参数的值，则会将 VM 添加到默认的*AAD DC 计算机*OU。 |

1. 查看条款和条件，并选中 "**我同意上述条款和条件**" 复选框。 准备就绪后，选择 "**购买**" 以将 VM 加入到 Azure AD DS 托管域。

> [!WARNING]
> **암호 처리에 주의하세요.**
> 模板参数文件请求作为*AZURE AD DC administrators*组成员的用户帐户的密码。 不要手动向此文件中输入值，而是将其保留在文件共享或其他共享位置。

完成部署需要几分钟时间。 完成后，指定的 Windows Vm 将加入到 Azure AD DS 托管域，并可使用域帐户进行管理或登录。

## <a name="next-steps"></a>다음 단계

本文介绍了如何使用 Azure 门户通过模板配置和部署资源。 你还可以使用[Azure PowerShell][deploy-powershell]或[Azure CLI][deploy-cli]部署具有资源管理器模板的资源。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
