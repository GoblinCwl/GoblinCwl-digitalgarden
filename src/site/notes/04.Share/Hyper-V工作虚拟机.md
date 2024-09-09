---
{"dg-publish":true,"permalink":"/04.Share/Hyper-V工作虚拟机/"}
---

## 使用场景
个人笔记本独立区分日常使用和工作使用场景，如只在工作虚拟机上安装工作软件（公司OA、各项目VPN软件、公司安全管理软件等），有些公司需要安装的软件往往伴随着检测、无法卸载、无法禁止自启动、关闭自启动后无法正常使用、VPN软件导致过多虚拟网卡等。以及公司网络与个人使用网络区分（内网环境、VPN断网不影响宿主机等），**需要电脑处理器比较强大，需要内存容量较大，需要足够的硬盘空间**。
![个人宿主机|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091019835.png)![工作虚拟机|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091016733.png)

> 什么是虚拟机？
> 摘自百度百科：虚拟机（Virtual Machine）指通过软件模拟的具有完整硬件系统功能的、运行在一个完全隔离环境中的完整计算机系统。在实体计算机中能够完成的工作在虚拟机中都能够实现。在计算机中创建虚拟机时，需要将实体机的部分硬盘和内存容量作为虚拟机的硬盘和内存容量。每个虚拟机都有独立的CMOS、硬盘和操作系统，可以像使用实体机一样对虚拟机进行操作。

## 实现内容
- 安装Hyper-V虚拟机
- 硬件挂载
- 静态IP和网络互通
- 帧数提升
- 清晰度提升
- 音频质量提升
- 独显虚拟化
- 网卡直连
- 快捷切换使用

> 为什么选用Hyper-V？ 
> 笔主有使用Linux子系统（WSL）的需求，故不考虑其他选择

## 安装虚拟机
1. 首先需要启动Windows自带的Hyper-V来安装虚拟机； 
打开**控制面板**，选择“**程序和功能**”>“**启用或关闭 Windows 功能**”，勾选“**Hyper-V**”后确定保存。
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091027733.png)
> 如果这一步没有Hyper-V选项，可能是Windows版本类型问题，尝试搜索解决
2. 选择合适的镜像
个人推荐使用LTSC版本的Windows作为虚拟机的系统，Win10和Win11都可以，笔主使用的是Win11 LTSC
Win10 LTSC: https://www.imsdn.cn/windows-10/win10-ltsc/
Win11 LTSC: 暂无公共资源，可以从B站或者一些论坛找资源下载
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091042550.png)
> LTSC：LTSC 是 Windows 操作系统的一个特殊版本，功能更新频率低，砍掉了很多内置应用，可以根据需要选择是否安装功能更新，可以理解为精简版Windows
3. 安装虚拟机
打开Hyper-V管理器，右侧新建->新建虚拟机
按照流程依次设置
名称（起一个自己能看懂的名字标识符）、
存储位置（虚拟机文件存放在宿主机的哪个目录）、
代数（我选的1代，2代装起来有点问题。。）、
内存（我40G内存分了20G给虚拟机，根据自己的使用情况决定）、
网络（Default Switch）、
创建虚拟硬盘（根据你的需要分配容量）、
**在安装选项时，选择下载的系统镜像iso文件**、
然后点击完成
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091044237.png)
右键刚刚创建的虚拟机，为CPU分配多个内核（我宿主机16核CPU，我直接给虚拟机也分配16核了，根据自己的使用情况决定）
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091435498.png)
然后启动虚拟机，开始根据Windows安装向导安装系统，安装完后就可以进入虚拟机系统了
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091047938.png)

## 增强模式
开启虚拟机增强会话模式，增强会话模式可以在连接窗口随意调整虚拟机分辨率，以及连接宿主机硬件设备
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091049073.png)
为了可以访问宿主机的硬盘、U盘等，需要右键虚拟机，点击编辑会话设置
在弹出的窗口中选择本地资源，然后分别设置
远程音频，虚拟机可以使用宿主机的扬声器和麦克风
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091051916.png)
本地资源和设备，虚拟机可以使用宿主机的硬盘（网络硬盘形式）和一些支持的硬件（如摄像头）
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091050113.png)![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091432175.png)
或者在后文使用远程桌面连接（或者其他工具）时根据对应软件设置


## 静态IP地、网络互通
在Hyper-V管理器右侧界面选择虚拟交换机管理器，新建一个内部交换机，记住这个名字
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091059107.png)
进入宿主机的控制面板->网络和Internet->网络和共享中心->（左侧）更改适配器设置，找到“vEthernet (你刚刚新建的交换机名字)”，手动配置IPv4地址
如此处我设置的是IP地址：`192.168.10.1`，子网掩码设置为`255.255.255.0`
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091101461.png)
进入Hyper-V管理器，右键你的虚拟机，点击设置，添加一个网络适配器，选中你之前新建的虚拟交换机的名字
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091102827.png)![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091104844.png)
**进入虚拟机**的控制面板->网络和Internet->网络和共享中心->（左侧）更改适配器设置，找到新增加的网络适配器
按照同样步骤设置IP地址，设置为`192.168.10.2`，默认网关地址设置为宿主机设置的地址（一般情况下为Microsoft Hyper-V Network Adapter xxx）
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091105172.png)
以上设置完成后，虚拟机的IP地址被固定为`192.168.10.2`，并且可以通过`192.168.10.1`访问宿主机网络（需要注意Windows防火墙是否放行）

## 使用远程桌面连接
为了更方便和更多的配置项，可以使用专用的远程桌面软件通过内网IP连接虚拟机，此处使用Windows自带的远程桌面，直接搜索就有
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091109212.png)
输入你的虚拟机内网IP即可连接，需要认证用户凭证 
由于虚拟机内网对网络基本无要求，可以直接拉满画质体验
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091111393.png)![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091348171.png)
在本地资源页同样配置上文中增强模式提到的远程音频和本地资源和设备
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091112606.png)
如果明明已经保存用户凭据，但是连接还是需要输入密码，并且提示“Windows Defender Credential Guard不允许使用已保存的凭据”
可尝试管理员运行命令提示符（CMD）输入以下命令：
`cmdkey /generic:TERMSRV/<虚拟机IP> /user:<虚拟机系统用户名> /pass:<虚拟机系统密码>`
![](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091114005.png)
设置完毕后通过另存为RDP文件，放在一个合适的位置（不要删除），然后创建快捷方式，用来快速链接虚拟机
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091423427.png)![动画.gif|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091118894.gif)

## 远程桌面的画质、音频质量
1. 帧数
**虚拟机**注册表打开`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations`
新建DWORD（32位）值，命名为`DWMFRAMEINTERVAL`
选择十进制，输入8，连接为120fps左右
选择十六进制，输入15，连接为60fps左右
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091421086.png)![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091422970.png)
重启虚拟机生效
测试帧数网址：https://testufo.com/

2. 画质
**虚拟机**win+r输入`gpedit.msc`，定位到管理模板->Windows组件->远程桌面服务->远程会话主机->远程会话环境
按图设置
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091428482.png)
重启虚拟机生效

3. 音质
虚拟机会有爆音问题，以下设置解决
**虚拟机**win+r输入`gpedit.msc`，定位到管理模板->Windows组件->远程桌面服务->远程会话主机->设备和资源重定向 
设置为高
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091430707.png)
用记事本（或者其他编辑工具）打开上文中保存的rdp文件，在最后加上一行`audioqualitymode:i:2`
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091431092.png)
重启虚拟机生效

## 独显虚拟化
1. 虚拟机关机 
![image.png|318](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091351972.png)
2. 关闭虚拟机检查点
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091351141.png)
3. 管理员打开CMD，**逐行**依次输入指令
```
$vm = "虚拟机的名字"
```
```
Add-VMGpuPartitionAdapter -VMName $vm
```
```
Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionVRAM 80000000 -MaxPartitionVRAM 100000000 -OptimalPartitionVRAM 100000000 -MinPartitionEncode 80000000 -MaxPartitionEncode 100000000 -OptimalPartitionEncode 100000000 -MinPartitionDecode 80000000 -MaxPartitionDecode 100000000 -OptimalPartitionDecode 100000000 -MinPartitionCompute 80000000 -MaxPartitionCompute 100000000 -OptimalPartitionCompute 100000000
```
```
Set-VM -GuestControlledCacheTypes $true -VMName $vm
```
```
Set-VM -LowMemoryMappedIoSpace 1Gb -VMName $vm
```
```
Set-VM -HighMemoryMappedIoSpace 32GB -VMName $vm
```
4. 将宿主机驱动程序拷贝到虚拟机
打开设备管理器，选中独立显卡，查看驱动程序
![image.png|218](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091354725.png)![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091355309.png)
滑动到最下面，有很多前缀相同的行，记住这个前缀，去下面这个目录找到和驱动程序中相同的文件夹
宿主机目录：`C:\Windows\System32\DriverStore\FileRepository\`
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091356408.png)
将整个文件夹复制到**虚拟机**的这个目录`C:\Windows\System32\HostDriverStore\FileRepository\`
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091359748.png)
- 如果是NVIDIA显卡
需要额外复制宿主机文件：`C:\Windows\System32\nvapi64.dll`到虚拟机：`C:\Windows\System32\nvapi64.dll`
- 如果是AMD显卡
则上文中独立显卡驱动程序详细信息里的，除了**已经**复制到的HostDriverStore里的一个文件夹，其他所有文件都要复制到虚拟机**相同**的文件夹里
![image.png|318](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091401143.png)![image.png|318](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091402974.png)
5. 检查虚拟机设备管理器
重启虚拟机后，此时虚拟机设备管理器中应有独立显卡
![image.png|318](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091404671.png)

>参考：
>https://www.bilibili.com/video/BV1Fu41177Xj/?share_source=copy_web&vd_source=0e955f10a214cd36e5e6354ce123f764
>https://www.freedidi.com/9857.html

**如果笔记本双显卡用户遇到虚拟机里出现的是核心显卡的情况，请开启独显直连（可能由笔记本品牌控制中心开启）并且在设备管理器中禁用核心显卡**

## 网卡直连
场景：使用公司网线（公司分发的IP地址、DNS地址等）时，需要软件登陆验证，希望虚拟机使用公司网线，而宿主机使用其他网卡
宿主机打开控制面板->网络和Internet->网络和共享中心->（左侧）更改适配器设置，找到公司网络的适配器，更改IPv4地址为自动获取
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091414150.png)
进入Hyper-V管理器，右侧虚拟交换机管理器，创建虚拟交换机，选择外部，选择连接公司网络的网卡
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091417323.png)
进入工作虚拟机的设置，添加网络适配器，选中公司网络的适配器名称
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091413950.png)
进入**虚拟机**，禁用默认的网络驱动器（Default-Switch）
![image.png|318](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091415225.png)
**虚拟机**内配置刚刚新增的网络适配器，配置为公司网络所用的IP地址
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091418705.png)
至此，已经可以通过公司网络正常上网，外部宿主机不受影响（但是会存在适配器）

## 快捷切换使用 
可以在宿主机创建一个虚拟桌面（Win + Tab下面的新建桌面），用来专门打开工作虚拟机
![image.png|518](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091438622.png)
- 使用键盘切换
Ctrl + Win + →/←
操作虚拟机时不好使，可能需要额外配置
- 使用触摸板切换
四根手指左右滑动
- **使用鼠标侧键切换（推荐）**
项目地址：https://github.com/march1993/win-sw-vd
下载release后将exe放到一个合适的位置（不要删除）
分别配置左/右切换在你的鼠标侧键中，罗技为例
![image.png|500](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091442381.png)![image.png|500](https://cwl-img.oss-cn-beijing.aliyuncs.com/202409091442175.png)
