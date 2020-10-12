---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649125"
---
* **用户级凭据**：一组适用于整个 Azure 帐户的凭据。 需要部署到任何订阅（Azure 帐户有权对其进行访问）中的任何应用的应用服务时，可以使用这组凭据。 这是在门户 GUI（例如应用的[资源页](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)的“概览”和“属性”）中呈现的默认组。  当通过基于角色的访问控制 (RBAC) 或共同管理员权限授予用户应用访问权限时，该用户便可使用其用户级别的凭据，直到被撤销访问权限。 请勿与其他 Azure 用户共享这些凭据。

* **应用级凭据**：一组适用于每个应用的凭据。 若要只部署到该应用，则可使用这组凭据。 每个应用的凭据在其创建时自动生成。 这些凭据不能手动进行配置，但可随时进行重置。 如果要通过 (RBAC) 授予用户访问应用级别凭据的权限，该用户必须是应用的参与者或更高级别身份（包括网站参与者内置角色）。 读者不可进行发布，因此无法访问这些凭据。