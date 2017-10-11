1. 你想要保护的服务器上将复制到本地文件夹 (例如，/tmp) 安装。 在终端中运行以下命令：
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 若要安装移动服务，运行以下命令：

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. 安装完成后，移动服务将需要获取注册到配置服务器。 运行以下命令以注册配置服务器的移动服务。

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>移动服务安装程序命令行

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|参数|类型|描述|可能值|
|-|-|-|-|
|-r |强制|指定应安装移动服务 （毫秒），还是应安装 MasterTarget(MT)|MS </br> MT|
|-d |可选|移动服务的安装位置|/usr/local/ASR|
|-v|强制|指定获取在其安装移动服务的平台 </br> </br>- **VMware** ： 使用此值，如果你在运行的虚拟机上安装移动服务*VMware vSphere ESXi 主机*， *HYPER-V 主机*和*Phsyical 服务器* </br> - **Azure** ： 使用此值，如果你在 Azure IaaS VM 上安装代理| VMware </br> Azure|
|-q|可选|指定要在静默模式下运行安装程序| 不适用|


#### <a name="mobility-service-configuration-command-line"></a>移动服务配置命令行

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|参数|类型|描述|可能值|
|-|-|-|-|
|-i |强制|配置服务器 IP|任何有效的 IP 地址|
|-P |强制|完整文件路径保存连接密码的文件|任何有效的文件夹|
