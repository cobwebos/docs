<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>连接 SAS 电缆
1. 识别主机箱与 EBOD 机箱。 可以通过查看相应的背板来识别这两个机箱。 请参考下图。 
   
    ![主机箱与 EBOD 机箱的背板](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **主机箱与 EBOD 机箱的后视图**
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |主机箱 |
   | 2 |EBOD 机箱 |
2. 找到主机箱与 EBOD 机箱的序号。 序号标签粘贴在每个机箱的背面。 两个机箱上的序号必须相同。 如果序号不相同，请立即[联系 Microsoft 支持](../articles/storsimple/storsimple-contact-microsoft-support.md)。 参考下图找到序号。
   
    ![显示序号的机箱后视图](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **序号标签位置**
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |机箱的吊耳 |
3. 接下来，使用提供的 SAS 电缆连接主机箱与 EBOD 机箱，如下所示：
   
   1. 识别主机箱与 EBOD 机箱上的四个 SAS 端口。 SAS 端口在主机箱上标记为 EBOD，对应于 EBOD 机箱上的端口 A，如下面的 SAS 布线图中所示。
   2. 使用提供的 SAS 电缆连接 EBOD 端口与端口 A。
   3. 控制器 0 的 EBOD 端口应连接到 EBOD 控制器 0 的端口 A。 控制器 1 的 EBOD 端口应连接到 EBOD 控制器 1 的端口 A。 请参考下图。 
      
      ![设备的 SAS 布线](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS 布线**
      
      | 标签 | 说明 |
      |:--- |:--- |
      | A |主机箱 |
      | B |EBOD 机箱 |
      | 1 |控制器 0 |
      | 2 |控制器 1 |
      | 3 |EBOD 控制器 0 |
      | 4 |EBOD 控制器 1 |
      | 5, 6 |主机箱上的 SAS 端口（标记为 EBOD） |
      | 7, 8 |EBOD 机箱上的 SAS 端口（端口 A） |

