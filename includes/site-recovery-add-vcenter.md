---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164906"
---
* 在“添加 vCenter”中，指定 vSphere 主机或 vCenter 服务器的友好名称，并指定服务器的 IP 地址或 FQDN。 除非已将 VMware 服务器配置为在不同的端口上侦听请求，否则请保留 443 作为端口号。 选择要连接到 VMware vCenter 或 vSphere ESXi 服务器的帐户。 单击“确定”。

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > 如果在添加 VMware vCenter 服务器或 VMware vSphere 主机时使用的帐户没有对 vCenter 或主机服务器的管理员权限，请确保为帐户启用了以下权限：数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。 此外，VMware vCenter 服务器需要启用存储视图权限。
