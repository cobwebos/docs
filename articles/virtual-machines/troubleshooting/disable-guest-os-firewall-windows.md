---
title: Azure VM에서 게스트 OS 방화벽 사용 안 함 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 292b53fac6c970fb961e8ad4ce7774c080e52422
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718865"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Azure VM에서 게스트 OS 방화벽 사용 안 함

이 문서에서는 게스트 운영 체제 방화벽이 VM(가상 머신) 트래픽의 일부 또는 전체를 필터링하는 것으로 의심되는 상황에 대한 참조를 제공합니다. 이 문제는 방화벽에서 RDP 연결 오류를 일으키는 변경 작업이 의도적으로 수행된 경우에 발생할 수 있습니다.

## <a name="solution"></a>솔루션

이 문서에서 설명하는 프로세스는 문제를 해결하는 용도로 사용되므로 실제 문제 해결, 즉, 방화벽 규칙을 올바르게 설정하는 방법에 집중할 수 있습니다. Microsoft 最佳做法是启用 Windows 防火墙组件。 配置防火墙规则的方式取决于对所需 VM 的访问级别。

### <a name="online-solutions"></a>온라인 솔루션 

VM이 온라인 상태이고 동일한 가상 네트워크의 다른 VM에서 액세스할 수 있는 경우 다른 VM을 사용하여 문제를 완화할 수 있습니다.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>해결 방법 1: 사용자 지정 스크립트 확장 또는 명령 실행 기능

Azure 에이전트가 작동 중인 경우 [사용자 지정 스크립트 확장](../extensions/custom-script-windows.md) 또는 [명령 실행](../windows/run-command.md) 기능을 사용하여(Resource Manager VM만 해당) 원격으로 다음 스크립트를 실행할 수 있습니다.

> [!Note]
> * 방화벽이 로컬로 설정된 경우 다음 스크립트를 실행합니다.
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * 방화벽이 Active Directory 정책을 통해 설정된 경우 다음 스크립트를 실행하여 임시로 액세스할 수 있습니다. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   그러나 정책이 다시 적용되는 즉시 원격 세션이 종료됩니다. 이 문제를 영구적으로 해결하는 방법은 이 컴퓨터에 적용되는 정책을 수정하는 것입니다.

#### <a name="mitigation-2-remote-powershell"></a>해결 방법 2: 원격 PowerShell

1.  RDP 연결을 사용하여 연결할 수 없는 VM과 동일한 가상 네트워크에 있는 VM에 연결합니다.

2.  PowerShell 콘솔 창을 엽니다.

3.  다음 명령을 실행합니다.

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> 如果通过组策略对象设置防火墙，则此方法可能不起作用，因为此命令只更改本地注册表项。 정책을 적용하면 이 변경 내용이 재정의됩니다. 

#### <a name="mitigation-3-pstools-commands"></a>해결 방법 3: PSTools 명령

1.  문제 해결을 위한 VM에서 [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)를 다운로드합니다.

2.  CMD 인스턴스를 열고 해당 DIP를 통해 VM에 액세스합니다.

3.  다음 명령을 실행합니다.

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>해결 방법 4: 원격 레지스트리 

다음 단계에 따라 [원격 레지스트리](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)를 사용합니다.

1.  문제 해결을 위한 VM에서 레지스트리 편집기를 시작한 다음, **파일** > **네트워크 레지스트리 연결**로 이동합니다.

2.  打开 "*目标计算机*\SYSTEM" 分支，并指定以下值：

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  서비스를 다시 시작합니다. 이 작업은 원격 레지스트리를 사용할 수 없으므로 서비스 제거 콘솔을 사용해야 합니다.

4.  打开**services.msc**的实例。

5.  **서비스(로컬)** 을 클릭합니다.

6.  **다른 컴퓨터에 연결**을 선택합니다.

7.  输入问题 VM 的**专用 IP 地址（DIP）** 。

8.  로컬 방화벽 정책을 다시 시작합니다.

9.  로컬 컴퓨터에서 다시 RDP를 통해 VM에 연결합니다.

### <a name="offline-solutions"></a>오프라인 솔루션 

어떤 방법으로도 VM에 연결할 수 없는 경우 사용자 지정 스크립트 확장이 실패할 것이며, 시스템 디스크를 통해 직접 작업하여 오프라인 모드로 작업해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1.  [복구 VM에 시스템 디스크 연결](troubleshoot-recovery-disks-portal-windows.md).

2.  복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

3.  디스크 관리 콘솔에서 디스크의 플래그가 [온라인]으로 지정되었는지 확인합니다. 연결된 시스템 디스크에 할당된 드라이브 문자를 적어 둡니다.

4.  변경 내용을 롤백해야 하는 경우를 대비하여 변경 전에 \windows\system32\config 폴더의 복사본을 만듭니다.

5.  문제 해결을 위한 VM에서 레지스트리 편집기(regedit.exe)를 시작합니다. 

6.  이 문제 해결 절차에서는 하이브를 BROKENSYSTEM 및 BROKENSOFTWARE로 탑재합니다.

7.  HKEY_LOCAL_MACHINE 키를 강조 표시한 다음, 메뉴에서 파일 > Hive 로드를 차례로 선택합니다.

8.  연결된 시스템 디스크에서 \windows\system32\config\SYSTEM 파일을 찾습니다.

9.  관리자 권한 PowerShell 인스턴스를 열고 다음 명령을 실행합니다.

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [시스템 디스크를 분리하고 VM을 다시 만듭니다](troubleshoot-recovery-disks-portal-windows.md).

11. 문제가 해결되었는지 확인합니다.
