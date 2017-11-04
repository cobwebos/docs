---
title: "使用门户管理 Azure 堆栈中的密钥保管库 |Microsoft 文档"
description: "了解如何通过使用门户管理 Azure 堆栈中的密钥保管库"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d263cbcc81be37eaedfdb771436fd13ef25362f8
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>使用门户管理 Azure 堆栈中的密钥保管库

你可以通过使用 Azure 堆栈门户管理 Azure 堆栈中的密钥保管库。 本文可帮助你开始创建和管理 Azure 堆栈中的密钥保管库。 

## <a name="prerequisites"></a>必备组件  

您必须订阅服务关联，它包含 Azure 密钥保管库服务。
 
## <a name="create-a-key-vault"></a>创建 key vault 

1. 登录到[用户门户](https://portal.local.azurestack.external)。  

2. 从仪表板中，选择**新建** > **安全性 + 标识** > **密钥保管库**。  

    ![密钥保管库屏幕](media/azure-stack-kv-manage-portal/image1.png)  

3. 在**创建密钥保管库**窗格中，将分配**名称**你的保管库。 保管库名称只能包含字母数字字符和特殊字符连字符 （-）。 它们不应以数字开头。  

4. 选择**订阅**从可用订阅列表。 下拉列表中显示提供密钥保管库服务的所有订阅。  

5. 选择现有**资源组**或创建一个新。  

6. 选择**定价层**。  
    >[!NOTE]
    > 密钥保管库中的 Azure 堆栈开发工具包支持**标准**仅 Sku。

7. 选择一个现有**访问策略**或创建一个新。 访问策略，可授予用户、 应用程序或安全组，以执行与此保管库操作的权限。  

8. （可选） 选择**高级访问权限策略**若要启用功能，用于部署的虚拟机 (Vm) 的访问，如访问到资源管理器模板部署，并访问 Azure 磁盘加密 for 卷加密。 
  
9.  配置设置后，选择**确定**，然后选择**创建**。 这将启动密钥保管库部署。 

## <a name="manage-keys-and-secrets"></a>管理密钥和机密

创建保管库后，使用以下步骤来创建和管理密钥和机密在保管库中。

### <a name="create-a-key"></a>创建密钥

1. 登录到[用户门户](https://portal.local.azurestack.external)。  

2. 从仪表板中，选择**的所有资源**，选择你以前创建的密钥保管库，然后选择**密钥**磁贴。  

3. 在**密钥**窗格中，选择**添加**。 

4. 在**创建密钥**窗格中，从列表中**选项**，选择你想要用于创建密钥的方法。 你可以**生成**新密钥，**上载**现有键，或使用**备份还原**选择密钥的备份。  

5. 输入**名称**你的密钥。 密钥名称只能包含字母数字字符和特殊字符连字符 （-）。  

6. （可选） 配置**设置激活日期**和**设置过期日期**密钥值。  

7. 选择**创建**启动部署。  

已成功创建密钥后，可以选择其在**密钥**和查看或修改其属性。 属性部分包含**密钥标识符**，即外部应用程序可以访问此密钥统一资源标识符 (URI)。 若要限制对此项操作，配置下的设置**允许的操作**。

![URI 的密钥](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>创建机密 

1. 登录到[用户门户](https://portal.local.azurestack.external)。  
2. 从仪表板中，选择**的所有资源**，选择你以前创建的密钥保管库，然后选择**机密**磁贴。  

3. 下**机密**，选择**添加**。  

4. 下**创建密钥**，从列表中**上载选项**，选择你想要创建密钥的一个选项。 你可以创建一个机密**手动**如果机密或上载输入值**证书**来自本地计算机。  

5. 输入**名称**的机密。 机密的名称只能包含字母数字字符和特殊字符连字符 （-）。  

6. （可选） 指定**内容类型**，和配置的值**设置激活日期**和**设置过期日期**的机密。  

7. 选择**创建**启动部署。  

已成功创建密钥后，可以选择其在**机密**和查看或修改其属性。 属性部分包含**机密标识符**，即外部应用程序可以访问此密钥的 URI。 

![URI 机密](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>后续步骤
* [将 VM 部署通过检索密钥保管库中存储的密码](azure-stack-kv-deploy-vm-with-secret.md) 
* [使用证书存储在密钥保管库中部署 VM](azure-stack-kv-push-secret-into-vm.md)     


