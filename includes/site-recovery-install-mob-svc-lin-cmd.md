---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301439"
---
1. 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 /tmp）。 在终端运行以下命令：
     ```
     cd /tmp;
     tar -xvzf Microsoft-ASR_UA*release.tar.gz
     ```
2. 若要安装移动服务，请运行以下命令：

     ```
     sudo ./install -d <Install Location> -r MS -v VmWare -q
     ```
3. 安装完成后，必须将移动服务注册到配置服务器。 运行以下命令，将移动服务注册到配置服务器：

     ```
     /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
     ```

#### <a name="mobility-service-installer-command-line"></a>移动服务安装程序命令行

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|参数|Type|描述|可能的值|
|-|-|-|-|
|-r |必需|指定是应安装移动服务 (MS) 还是 MasterTarget(MT)。|MS <br /> MT|
|-d |可选|移动服务安装到的位置。|/usr/local/ASR|
|-v|必需|指定一个平台，以便在其上安装移动服务。 <br /> <br />- **VMware**:如果在运行的虚拟机上安装移动服务，则使用该值*VMware vSphere ESXi 主机*， *HYPER-V 主机*，并*物理服务器*。 <br /> - **Azure**:如果在 Azure IaaS VM 上安装代理，请使用此值。| VMware <br /> Azure|
|-q|可选|指定在静默模式下运行安装程序。| 不适用|

#### <a name="mobility-service-configuration-command-line"></a>移动服务配置命令行

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|参数|Type|描述|可能的值|
|-|-|-|-|
|-i |必需|配置服务器的 IP|任何有效的 IP 地址|
|-P |必需|在其中保存连接密码的文件的完整文件路径|任何有效的文件夹|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->