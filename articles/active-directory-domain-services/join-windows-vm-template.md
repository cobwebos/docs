---
title: 使用模板将 Windows VM 加入 Azure AD DS |微软文档
description: 了解如何使用 Azure 资源管理器模板将新的或现有的 Windows 服务器 VM 加入 Azure 活动目录域服务托管域。
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
ms.openlocfilehash: c9b25fe7bc47e05972aebb194e9d94c1ea6dd247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298728"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>使用资源管理器模板将 Windows 服务器虚拟机加入 Azure 活动目录域服务托管域

要自动部署和配置 Azure 虚拟机 （VM），可以使用资源管理器模板。 这些模板允许您每次创建一致的部署。 扩展也可以包含在模板中，以自动配置 VM 作为部署的一部分。 一个有用的扩展将 VM 连接到域，该域可与 Azure 活动目录域服务 （Azure AD DS） 托管域一起使用。

本文演示如何使用资源管理器模板创建 Windows 服务器 VM 并将其加入 Azure AD DS 托管域。 您还了解如何将现有的 Windows 服务器 VM 加入 Azure AD DS 域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 属于 Azure AD DS 托管域的用户帐户。

## <a name="azure-resource-manager-template-overview"></a>Azure 资源管理器模板概述

资源管理器模板允许您在代码中定义 Azure 基础结构。 所需的资源、网络连接或 VM 配置都可以在模板中定义。 这些模板每次都创建一致、可重复的部署，并在进行更改时进行版本控制。 有关详细信息，请参阅[Azure 资源管理器模板概述][template-overview]。

每个资源都使用 JavaScript 对象表示法 （JSON） 在模板中定义。 以下 JSON 示例使用*Microsoft.计算/虚拟机/扩展*资源类型来安装活动目录域联接扩展。 使用您在部署时指定的参数。 部署扩展时，VM 将连接到指定的 Azure AD DS 托管域。

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

即使您不在同一模板中创建 VM，也可以部署此 VM 扩展。 本文中的示例显示了以下两种方法：

* [创建 Windows 服务器 VM 并加入托管域](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [将现有的 Windows Server VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>创建 Windows 服务器 VM 并加入托管域

如果需要 Windows 服务器 VM，可以使用资源管理器模板创建和配置一个 VM。 部署 VM 时，将安装扩展以将 VM 加入 Azure AD DS 托管域。 如果您已经有一个 VM，您希望加入 Azure AD DS 托管域，请跳至[将现有的 Windows 服务器 VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)。

要创建 Windows 服务器 VM，然后将其加入 Azure AD DS 托管域，请完成以下步骤：

1. 浏览到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。 选择要**部署到 Azure**的选项。
1. 在 **"自定义部署"** 页上，输入以下信息以创建 Windows 服务器 VM 并将其加入到 Azure AD DS 托管域：

    | 设置                   | “值” |
    |---------------------------|-------|
    | 订阅              | 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。 |
    | 资源组            | 选择 VM 的资源组。 |
    | 位置                  | 选择 VM 的位置。 |
    | 现有 VNET 名称        | 要将 VM 连接到的现有虚拟网络的名称，如*myVnet*。 |
    | 现有子网名称      | 现有虚拟网络子网的名称，如*工作负载*。 |
    | DNS 标签前缀          | 输入用于 VM 的 DNS 名称，如*myvm*。 |
    | VM 大小                   | 指定 VM 大小，如*Standard_DS2_v2*。 |
    | 要加入的域            | Azure AD DS 托管域 DNS 名称，如*aaddscontoso.com*。 |
    | 域用户名           | 应用于将 VM 加入托管域（如`contosoadmin@aaddscontoso.com`） 的 Azure AD DS 托管域中的用户帐户。 此帐户必须是 Azure AD DS 托管域的一部分。 |
    | 域密码           | 上一个设置中指定的用户帐户的密码。 |
    | 可选的 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果不为此参数指定值，则 VM 将添加到默认*的 AAD DC 计算机*OU 中。 |
    | VM 管理员用户名         | 指定要在 VM 上创建的本地管理员帐户。 |
    | VM 管理员密码         | 为 VM 指定本地管理员密码。 创建强大的本地管理员密码，以防止密码暴力攻击。 |

1. 查看条款和条件，然后选中"**我同意上述条款和条件"复选框**。 准备就绪后，选择 **"购买"** 以创建 VM 并将其加入 Azure AD DS 托管域。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件请求属于 Azure AD DS 托管域的用户帐户的密码。 不要手动在此文件中输入值，使其在文件共享或其他共享位置上访问。

成功完成部署需要几分钟时间。 完成后，将创建 Windows VM 并加入 Azure AD DS 托管域。 可以使用域帐户管理 VM 或登录 VM。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>将现有的 Windows Server VM 加入托管域

如果现有 VM 或 VM 组希望加入 Azure AD DS 托管域，则可以使用资源管理器模板来仅部署 VM 扩展。

要将现有的 Windows 服务器 VM 加入 Azure AD DS 托管域，完成以下步骤：

1. 浏览到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。 选择要**部署到 Azure**的选项。
1. 在 **"自定义部署"** 页上，输入以下信息以将 VM 加入 Azure AD DS 托管域：

    | 设置                   | “值” |
    |---------------------------|-------|
    | 订阅              | 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。 |
    | 资源组            | 选择具有现有 VM 的资源组。 |
    | 位置                  | 选择现有 VM 的位置。 |
    | VM 列表                   | 输入要加入 Azure AD DS 托管域的现有 VM 的逗号分隔列表，例如*myVM1，myVM2*。 |
    | 域加入用户名     | 应用于将 VM 加入托管域（如`contosoadmin@aaddscontoso.com`） 的 Azure AD DS 托管域中的用户帐户。 此帐户必须是 Azure AD DS 托管域的一部分。 |
    | 域加入用户密码 | 上一个设置中指定的用户帐户的密码。 |
    | 可选的 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果不为此参数指定值，则 VM 将添加到默认*的 AAD DC 计算机*OU 中。 |

1. 查看条款和条件，然后选中"**我同意上述条款和条件"复选框**。 准备就绪后，选择 **"购买"** 以将 VM 加入 Azure AD DS 托管域。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件请求属于 Azure AD DS 托管域的用户帐户的密码。 不要手动在此文件中输入值，使其在文件共享或其他共享位置上访问。

成功完成部署需要一些时间。 完成后，指定的 Windows VM 将连接到 Azure AD DS 托管域，并且可以使用域帐户进行管理或签名。

## <a name="next-steps"></a>后续步骤

在本文中，您可以使用 Azure 门户使用模板配置和部署资源。 您还可以使用[Azure PowerShell][deploy-powershell]或[Azure CLI][deploy-cli]使用资源管理器模板部署资源。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
