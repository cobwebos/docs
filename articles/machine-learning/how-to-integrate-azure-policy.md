---
title: 审核和管理 Azure 策略的符合性
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 策略将内置策略用于 Azure 机器学习。
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994640"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>使用 Azure 策略审核和管理 Azure 机器学习

[Azure 策略](/azure/governance/policy) 是一种管理工具，可让你确保 Azure 资源符合你的策略。 通过 Azure 机器学习，您可以分配以下策略：

* **客户托管的密钥**：审核或强制工作区是否必须使用客户管理的密钥。
* **专用链接**：审核工作区是否使用专用终结点与虚拟网络通信。

可以在不同的作用域（如订阅或资源组级别）设置策略。 有关详细信息，请参阅 [Azure 策略文档](/azure/governance/policy/overview)。

## <a name="built-in-policies"></a>内置策略

Azure 机器学习提供了一组可用于使用 Azure 机器学习的常见方案的策略。 您可以将这些策略定义分配给现有订阅，也可以将它们用作基础来创建自己的自定义定义。 有关 Azure 机器学习的内置策略的完整列表，请参阅 [Azure 机器学习的内置策略](/azure/governance/policy/samples/built-in-policies#machine-learning)。

若要查看与 Azure 机器学习相关的内置策略定义，请使用以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，请参阅__Azure 策略__。
1. 选择“定义”。
1. 对于 " __类型__"，选择 " _内置_"，对于 " __类别__"，选择 " __机器学习__"。

在此处，你可以选择策略定义来查看它们。 查看定义时，可以使用 " __分配__ " 链接将策略分配到特定的作用域，并为策略配置参数。 有关详细信息，请参阅 [分配策略-门户](/azure/governance/policy/assign-policy-portal)。

你还可以使用 [Azure PowerShell](/azure/governance/policy/assign-policy-powershell)、 [Azure CLI](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)和 [模板](/azure/governance/policy/assign-policy-template)分配策略。

## <a name="workspaces-encryption-with-customer-managed-key"></a>客户管理的密钥的工作区加密

控制工作区是否应使用客户管理的密钥 (CMK) 进行加密，或者使用 Microsoft 托管的密钥来加密度量值和元数据。 有关使用 CMK 的详细信息，请参阅企业安全文章的 [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) 部分。

若要配置此策略，请将 effect 参数设置为 __audit__ 或 __deny__。 如果设置为 " __审核__"，则可以创建不带 CMK 的工作区，并且会在活动日志中创建一个警告事件。

如果将策略设置为 " __拒绝__"，则无法创建工作区，除非该工作区指定了 CMK。 如果尝试在不使用 CMK 的情况下创建工作区，将导致类似于的错误 `Resource 'clustername' was disallowed by policy` ，并会在活动日志中创建一个错误。 策略标识符也作为此错误的一部分返回。

## <a name="workspaces-should-use-private-link"></a>工作区应使用专用链接

控制工作区是否应使用 Azure 专用链接来与 Azure 虚拟网络通信。 有关使用专用链接的详细信息，请参阅 [配置工作区的专用链接](how-to-configure-private-link.md)。

若要配置此策略，请将 effect 参数设置为 __audit__。 如果不使用 "专用" 链接创建工作区，则会在活动日志中创建一个警告事件。

## <a name="next-steps"></a>后续步骤

* [Azure Policy 文档](/azure/governance/policy/overview)
* [Azure 机器学习的内置策略](policy-reference.md)
* [使用 Azure 安全中心处理安全策略](/azure/security-center/tutorial-security-policy)