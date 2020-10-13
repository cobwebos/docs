---
title: 使用模板将 Windows VM 加入到 Azure AD DS | Microsoft Docs
description: 了解如何使用 Azure 资源管理器模板，将新的或现有的 Windows Server VM 加入到 Azure Active Directory 域服务托管域。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 988f009527f26a9f2be965b635d57f0bc38913c2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960688"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>使用资源管理器模板，将 Windows Server 虚拟机加入到 Azure Active Directory 域服务托管域

若要自动执行 Azure 虚拟机 (VM) 的部署和配置，可使用资源管理器模板。 通过这些模板，可每次创建一致的部署。 扩展也可以包含在模板中，以在部署过程中自动配置 VM。 一个实用的扩展可将 VM 加入到域中，该域可与 Azure Active Directory 域服务 (Azure AD DS) 托管域一起使用。

本文介绍如何使用资源管理器模板创建 Windows Server VM 并将它加入到 Azure AD DS 托管域。 你还可了解如何将现有的 Windows Server VM 加入到 Azure AD DS 域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 属于托管域的用户帐户。

## <a name="azure-resource-manager-template-overview"></a>Azure 资源管理器模板概述

通过资源管理器模板，可在代码中定义 Azure 基础结构。 所有必需的资源、网络连接或 VM 配置均可在模板中进行定义。 这些模板每次都创建一致的可重现部署，并且可以在你进行更改时进行版本控制。 有关详细信息，请参阅 [Azure 资源管理器模板概述][template-overview]。

使用 JavaScript 对象表示法 (JSON) 在模板中定义每个资源。 以下 JSON 示例使用 Microsoft.Compute/virtualMachines/extensions 资源类型来安装 Active Directory 域加入扩展。 使用在部署时指定的参数。 扩展部署完成后，VM 加入到指定的托管域。

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

即使你不在同一个模板中创建 VM，此 VM 扩展也可进行部署。 本文中的示例演示了以下两种方法：

* [创建 Windows Server VM 并将其加入托管域](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [将现有的 Windows Server VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>创建 Windows Server VM 并将其加入托管域

如果你需要 Windows Server VM，可使用资源管理器模板创建并配置一个。 VM 部署后，安装一个扩展以将 VM 加入托管域。 如果你已有要加入托管域的 VM，请跳至[将现有 Windows Server VM 加入托管域](#join-an-existing-windows-server-vm-to-a-managed-domain)。

若要创建一个 Windows Server VM，然后将其加入托管域，请完成以下步骤：

1. 浏览到[快速启动模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。 选择“部署到 Azure”选项。
1. 在“自定义部署”页上，输入以下信息以创建 Windows Server VM 并将其加入托管域：

    | 设置                   | Value |
    |---------------------------|-------|
    | 订阅              | 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。 |
    | 资源组            | 选择 VM 的资源组。 |
    | 位置                  | 选择 VM 的位置。 |
    | 现有 VNET 名称        | 要与 VM 连接的现有虚拟网络的名称，例如 myVnet。 |
    | 现有子网名称      | 现有虚拟网络子网的名称，例如 Workloads。 |
    | DNS 标签前缀          | 输入要用于 VM 的 DNS 名称，例如 myvm |
    | VM 大小                   | 指定 VM 规格，例如 Standard_DS2_v2。 |
    | 要加入的域            | 托管域 DNS 名称，例如 aaddscontoso.com。 |
    | 域用户名           | 托管域中的用户帐户（例如 `contosoadmin@aaddscontoso.com`），应使用该帐户将 VM 加入托管域。 此帐户必须属于托管域。 |
    | 域密码           | 在上一设置中指定的用户帐户的密码。 |
    | 可选 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果你未指定此参数的值，VM 会被添加到默认的“AAD DC 计算机”OU。 |
    | VM 管理员用户名         | 指定要在 VM 上创建的本地管理员帐户。 |
    | VM 管理员密码         | 为 VM 指定本地管理员密码。 创建本地管理员强密码，防范密码暴力破解攻击。 |

1. 查看条款和条件，然后勾选“我同意上述条款和条件”复选框。 准备就绪后，选择“购买”，创建 VM 并将其加入托管域。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件请求属于托管域的用户帐户的密码。 不要手动向此文件中输入值，也不要使它在文件共享或其他共享位置保持可访问状态。

需要几分钟才能成功完成部署。 完成后，Windows VM 已创建并已加入到托管域。 可使用域帐户管理或登录 VM。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>将现有的 Windows Server VM 加入托管域

如果你有要加入托管域的现有 VM 或 VM 组，可使用资源管理器模板仅部署 VM 扩展。

若要将现有的 Windows Server VM 加入托管域，请完成以下步骤：

1. 浏览到[快速启动模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。 选择“部署到 Azure”选项。
1. 在“自定义部署”页上，输入以下信息，将 VM 加入托管域：

    | 设置                   | Value |
    |---------------------------|-------|
    | 订阅              | 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。 |
    | 资源组            | 选择包含现有 VM 的资源组。 |
    | 位置                  | 选择现有 VM 的位置。 |
    | VM 列表                   | 输入要加入托管域的现有 VM 的逗号分隔列表，例如 myVM1,myVM2。 |
    | 域加入用户名     | 托管域中的用户帐户（例如 `contosoadmin@aaddscontoso.com`），应使用该帐户将 VM 加入托管域。 此帐户必须属于托管域。 |
    | 域加入用户密码 | 在上一设置中指定的用户帐户的密码。 |
    | 可选 OU 路径          | 要在其中添加 VM 的自定义 OU。 如果你未指定此参数的值，VM 会被添加到默认的“AAD DC 计算机”OU。 |

1. 查看条款和条件，然后勾选“我同意上述条款和条件”复选框。 准备就绪后，选择“购买”，将 VM 加入托管域。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件请求属于托管域的用户帐户的密码。 不要手动向此文件中输入值，也不要使它在文件共享或其他共享位置保持可访问状态。

需要一些时间才能成功完成部署。 完成后，指定的 Windows VM 将加入到托管域，你可使用域帐户管理或登录 VM。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure 门户通过模板配置和部署资源。 你也可使用 [Azure PowerShell][deploy-powershell] 或 [Azure CLI][deploy-cli]，通过资源管理器模板部署资源。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
