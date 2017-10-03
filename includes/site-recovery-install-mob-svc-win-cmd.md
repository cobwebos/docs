1. 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 C:\Temp）。 以管理员身份在命令提示符处运行以下命令：

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 若要安装移动服务，请运行以下命令：

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. 现在需将代理注册到配置服务器。

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>移动服务安装程序命令行参数

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| 参数|类型|说明|可能的值|
|-|-|-|-|
|/Role|必需|指定是应安装移动服务 (MS) 还是 MasterTarget(MT)|MS </br> MT|
|/InstallLocation|可选|移动服务安装到的位置|计算机上的任意文件夹|
|/Platform|必需|指定一个平台，以便在其上安装移动服务 </br> </br>- **VMware**：使用此值的前提是，在 VM 上安装移动服务，而该 VM 运行在 VMware vSphere ESXi 主机、Hyper-V 主机和物理服务器上 </br> - **Azure**：使用此值的前提是，在 Azure IaaS VM 上安装代理| VMware </br> Azure|
|/Silent|可选|指定在静默模式下运行安装程序| 不可用|

>[!TIP]
> 安装程序日志位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log

#### <a name="mobility-service-registration-command-line-arguments"></a>移动服务注册命令行参数

```
Usage :
UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | 参数|类型|说明|可能的值|
  |-|-|-|-|
  |/CSEndPoint |必需|配置服务器的 IP 地址| 任何有效的 IP 地址|
  |/PassphraseFilePath|必需|通行短语的位置 |任何有效的 UNC 或本地文件路径|


>[!TIP]
> AgentConfiguration 日志位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log
