---
title: 在 Azure 中为 SMTP 横幅检查配置反向查找区域 | Microsoft Docs
description: 介绍如何在 Azure 中为 SMTP 横幅检查配置反向查找区域
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151814"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>为 SMTP 横幅检查配置反向查找区域

本文介绍如何使用 Azure DNS 中的反向区域，以及如何为 SMTP 横幅检查创建反向 DNS (PTR) 记录。 

## <a name="symptom"></a>症状

如果在 Microsoft Azure 中托管 SMTP 服务器，则通过自远程邮件服务器收发邮件时，可能收到以下错误消息：

**554: 无 PTR 记录** 

## <a name="solution"></a>解决方案

对于 Azure 中的虚拟 IP 地址，将在 Microsoft 拥有的域区域（而不是自定义域区域）创建反向记录。

若要在 Microsoft 拥有区域配置 PTR 记录，请对 PublicIpAddress 资源使用 -ReverseFqdn 属性。 有关详细信息，请参阅[为 Azure 中托管的服务配置反向 DNS](../dns/dns-reverse-dns-for-azure-services.md)。 

配置 PTR 记录时，请确保 IP 地址和反向 FQDN 为订阅所有。 如果尝试设置不属于订阅的反向 FQDN，将收到以下错误消息：

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn 与订阅下的任意公共 IP 资源的 FQDN 相匹配； 
    2) ReverseFqdn 解析为订阅下任意公共 IP 资源的 FQDN（通过 CName 记录链）； 
    3) 解析为订阅下任意静态公共 IP 资源的 IP 地址（通过 CName 和 A 记录链）。 

如果将 SMTP 横幅手动更改为与默认反向 FQDN 相匹配，远程邮件服务器仍可能失败，因为它可能期望 SMTP 横幅主机与域的 MX 记录相匹配。