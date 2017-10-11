1. 你想要保护的服务器上将复制到本地文件夹 (例如，C:\Temp) 安装。 以管理员身份在命令提示符处运行以下命令：

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 若要安装移动服务，运行以下命令：

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. 现在需要配置服务器注册代理。

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>移动服务安装程序命令行参数

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| 参数|类型|描述|可能值|
|-|-|-|-|
|/ 角色|强制|指定应安装移动服务 （毫秒），还是应安装 MasterTarget(MT)|MS </br> MT|
|/ InstallLocation|可选|移动服务的安装位置|在计算机上任何文件夹|
|/ 平台|强制|指定获取在其安装移动服务的平台 </br> </br>- **VMware** ： 使用此值，如果你在运行的虚拟机上安装移动服务*VMware vSphere ESXi 主机*， *HYPER-V 主机*和*Phsyical 服务器* </br> - **Azure** ： 使用此值，如果你在 Azure IaaS VM 上安装代理| VMware </br> Azure|
|/Silent|可选|指定在静默模式下运行安装程序| NA|

>[!TIP]
> %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 下找不到安装程序日志

#### <a name="mobility-service-registration-command-line-arguments"></a>移动服务注册命令行自变量

```
Usage :
UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | 参数|类型|描述|可能值|
  |-|-|-|-|
  |/ CSEndPoint |强制|配置服务器的 IP 地址| 任何有效的 IP 地址|
  |/ PassphraseFilePath|强制|通行短语的位置 |任何有效的 UNC 或本地文件路径|


>[!TIP]
> %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 下找不到 AgentConfiguration 日志
