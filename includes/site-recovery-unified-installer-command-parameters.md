|参数名称| 类型 | 描述| 可能的值|
|-|-|-|-|
| / ServerMode|强制|指定是否应安装的配置和进程服务器，或仅在进程服务器|CS<br>PS|
|/ InstallLocation|强制|在其中安装组件的文件夹| 在计算机上任何文件夹|
|/ MySQLCredsFilePath|强制|在其中存储的 MySQL server 凭据的文件路径|应指定下面的格式文件。|
|/ VaultCredsFilePath|强制|保管库凭据文件的路径|有效的文件路径|
|/ EnvType|强制|你想要保护的环境的类型 |VMware<br>NonVMware|
|/ PSIP|强制|要用于复制数据传输的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/ CSIP|强制|配置服务器正在侦听的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/ PassphraseFilePath|强制|密码文件的位置的完整路径|有效的文件路径|
|/ BypassProxy|可选|指定配置服务器不使用代理连接到 Azure|若要执行从 Venu 获取此值|
|/ ProxySettingsFilePath|可选|（默认代理需要身份验证或自定义代理） 的代理设置|该文件应采用下面指定的格式|
|DataTransferSecurePort|可选|PSIP 要用于复制数据上的端口号| 有效端口号 （默认值为 9433）|
|/ SkipSpaceCheck|可选|跳过缓存磁盘空间检查| |
|/ AcceptThirdpartyEULA|强制|标志表示接受第三方 EULA| |
|/ ShowThirdpartyEULA|可选|显示第三方 EULA。 如果作为输入提供忽略所有其他参数| |
