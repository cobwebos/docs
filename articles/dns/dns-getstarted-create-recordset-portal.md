---
title: "使用 Azure 门户为 DNS 区域创建记录集和记录 | Microsoft Docs"
description: "如何使用 Azure 门户为 Azure DNS 创建主机记录和创建记录集和记录"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 194da45bade88c2866aaf6066d312d125f1ac2cb

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>使用 Azure 门户创建 DNS 记录集和记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文将指导你通过使用 Azure 门户完成创建记录和记录集过程。 在创建 DNS 区域后，为域添加 DNS 记录。 若要执行此操作，首先需要了解 DNS 记录和记录集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>创建记录集和记录

本文将指导你通过使用 Azure 门户完成创建记录和记录集过程。 我们将使用 DNS“A”记录类型。

1. 登录到门户。
2. 转到要在其中创建记录集的“**DNS 区域**”边栏选项卡。
3. 在“**DNS 区域**”边栏选项卡顶部，选择“**记录集**”以打开“**添加记录集**”边栏选项卡。

    ![新建记录集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. 在“**添加记录集**”边栏选项卡中，命名你的记录集。 例如，可将你的记录集命名为“**www**”。

    ![添加记录集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 选择想要创建的记录的类型。 例如，选择“**A**”。
6. 设置 **TTL**。 门户中的默认生存时间为一小时。
7. 以每行一个 IP 地址的方式，添加 IP 地址。 当使用前面所述的建议记录集名称和记录类型时，将 IPv4 IP 地址添加到 www 记录集的 **A** 记录。
8. 完成添加 IP 地址后，选择边栏选项卡底部的“**确定**”。 将创建 DNS 记录集。

## <a name="next-steps"></a>后续步骤

若要管理记录集和记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

有关 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。



<!--HONumber=Nov16_HO3-->


