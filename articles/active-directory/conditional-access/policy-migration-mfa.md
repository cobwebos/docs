---
title: 使用多重身份验证迁移条件访问策略-Azure Active Directory
description: 本文演示如何迁移要求在 Azure 门户中进行多重身份验证的经典策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027387"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>在 Azure 门户中迁移经典策略

本文演示如何迁移要求对云应用进行**多重身份验证**的经典策略。 虽然不是先决条件，但我们建议你在开始迁移经典策略之前阅读[在 Azure 门户中迁移经典策略](policy-migration.md)。

![Salesforce 应用需要进行 MFA 的经典策略详细信息](./media/policy-migration/33.png)

迁移过程包括以下步骤：

1. [打开经典策略](#open-a-classic-policy)获取配置设置。
1. 创建新的 Azure AD 条件访问策略以替换经典策略。 
1. 禁用经典策略。

## <a name="open-a-classic-policy"></a>打开经典策略

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“Azure Active Directory”   > “安全性”   > “条件访问”  。
1. 选择“经典策略”  。

   ![经典策略视图](./media/policy-migration-mfa/12.png)

1. 在经典策略列表中，选择要迁移的策略。 记录配置设置，以便可以使用新的条件访问策略重新创建。

## <a name="create-a-new-conditional-access-policy"></a>创建新的条件访问策略

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“Azure Active Directory”   > “安全性”   > “条件访问”  。
1. 若要创建新的条件访问策略，请选择“新建策略”  。
1. 在“新建”页上的“名称”文本框中，键入策略的名称。  
1. 在“分配”部分中，单击“用户和组”。  
   1. 如果已在经典策略中选择所有用户，请单击“所有用户”。  
   1. 如果已在经典策略中选择组，请单击“选择用户和组”，并选择所需的用户和组。 
   1. 如果需要排除组，请单击“排除”选项卡，并选择所需的用户和组。  
   1. 选择“完成” 
1. 在“分配”  部分中，单击“云应用或操作”  。
1. 在“云应用或操作”页上执行以下步骤： 
   1. 单击“选择应用”。 
   1. 单击“选择”  。
   1. 在“选择”页上选择云应用，单击“选择”。  
   1. 在“云应用”页上，单击“完成”。  
1. 如果已选择“需要多重身份验证”： 
   1. 在“访问控制”部分中，单击“授予”。  
   1. 在“授予”页上，依次单击“授予访问权限”、“需要多重身份验证”。   
   1. 单击“选择”  。
1. 单击“打开”  启用策略，然后选择“保存”  。

   ![创建条件访问策略](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>禁用经典策略

若要禁用经典策略，请单击“详细信息”视图中的“禁用”。

![禁用经典策略](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>后续步骤

- 有关经典策略迁移的详细信息，请参阅[在 Azure 门户中迁移经典策略](policy-migration.md)。
- [在仅限报告模式下进行条件访问来确定新策略决定的影响。](concept-conditional-access-report-only.md)