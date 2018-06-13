---
title: 使用门户管理 Azure Stack 中的 Key Vault | Microsoft Docs
description: 了解如何使用门户管理 Azure Stack 中的 Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: e387e365b2de3412c23c995939f75400a03eeb80
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070458"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>使用门户管理 Azure Stack 中的 Key Vault

可以使用 Azure Stack 门户管理 Azure Stack 中的 Key Vault。 本文可帮助你开始在 Azure Stack 中创建和管理密钥保管库。

## <a name="prerequisites"></a>必备组件

必须订阅包含 Azure Key Vault 服务的产品/服务。

## <a name="create-a-key-vault"></a>创建 key vault

1. 登录到[用户门户](https://portal.local.azurestack.external)。

2. 从仪表板中选择“新建” > “安全 + 标识” > “密钥保管库”。

    ![“密钥保管库”屏幕](media/azure-stack-kv-manage-portal/image1.png)

3. 在“创建密钥保管库”窗格中，为保管库分配**名称**。 保管库名称只能包含字母数字字符和特殊字符连字符 (-)。 它们不得以数字开头。

4. 从可用订阅列表中选择**订阅**。 下拉列表中将显示提供 Key Vault 服务的所有订阅。

5. 选择现有的**资源组**或创建一个新资源组。

6. 选择“定价层”。
    >[!NOTE]
    > Azure Stack 开发工具包中的密钥保管库仅支持**标准** SKU。

7. 选择一个现有**访问策略**或创建一个新访问策略。 使用访问策略，可授予用户、应用程序或安全组对此保管库执行操作的权限。

8. （可选） 选择**高级访问权限策略**以允许访问功能。 例如： 对于部署，资源管理器模板部署，以及对 Azure 磁盘加密卷加密的访问权限的虚拟机 (Vm)。

9. 配置设置后，请选择“确定”，然后选择“创建”。 这将启动密钥保管库部署。

## <a name="manage-keys-and-secrets"></a>管理密钥和机密

创建保管库后，使用以下步骤来创建并管理保管库中的密钥和机密。

### <a name="create-a-key"></a>创建密钥

1. 登录到[用户门户](https://portal.local.azurestack.external)。

2. 从仪表板中选择“所有资源”，选择先前创建的密钥保管库，然后选择“密钥”磁贴。

3. 在“密钥”窗格中，选择“添加”。

4. 在“创建密钥”窗格中，从“选项”列表中，选择要用于创建密钥的方法。 可以**生成**新密钥、**上传**现有密钥，或使用“备份还原”选择密钥的备份。

5. 输入密钥的**名称**。 密钥名称只能包含字母数字字符和特殊字符连字符 (-)。

6. （可选）为密钥配置**设置激活日期**和**设置到期日期**值。

7. 选择“创建”以开始部署。

成功创建密钥后，可以在“密钥”下选择该密钥，并查看或修改其属性。 属性部分包含**密钥标识符**，即外部应用程序用于访问此密钥统一资源标识符 (URI)。 若要限制对此密钥的操作，请在“允许的操作”下配置设置。

![密钥 URI](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>创建机密

1. 登录到[用户门户](https://portal.local.azurestack.external)。
2. 从仪表板中选择“所有资源”，选择先前创建的密钥保管库，然后选择“机密”磁贴。

3. 在“机密”下，选择“添加”。

4. 在“创建机密”下，从“上传选项”列表中，选择要用于创建机密的选项。 如果输入机密的值或从本地计算机上传**证书**，即可**手动**创建机密。

5. 输入机密的**名称**。 机密名称只能包含字母数字字符和特殊字符连字符 (-)。

6. （可选）指定**内容类型**，并为机密配置**设置激活日期**和**设置到期日期**的值。

7. 选择“创建”以开始部署。

成功创建机密后，可以在“机密”下选择该机密，并查看或修改其属性。 **机密标识符**是外部应用程序可用于访问此密钥的 URI。

![机密 URI](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>后续步骤

* [检索 Key Vault 中存储的密码来部署 VM](azure-stack-kv-deploy-vm-with-secret.md)
* [使用 Key Vault 中存储的证书来部署 VM](azure-stack-kv-push-secret-into-vm.md)
