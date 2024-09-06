# Xiaoxin-Pro16-Ryzen-Hackintosh 小新Pro16锐龙版（R5&R7）黑苹果双系统教程
> @利头霍斯 【少数派，CSDN，B站都同名】

### 1.镜像文件下载

* 比较建议下载全量包，最好dmg格式，这样至少能保证不会像在线安装那样慢或者出现什么问题

* 记得u盘分出一个分区做PE，后期有问题调试都方便，修改分区文件也直接去pe

### 2.Bios设置

* 关机--按开机后按住F键狂按F2
* 进入BIOS后总之要设置的有：**安全启动：关**；**U盘启动：开**；**快速启动：关**，怕反应不过来的话把U盘启动项改为第一项“BIOS界面有介绍各个F键的用法”，按F10保存，黑屏后按住F键狂按F12，就能看见白色的引导项界面
* 上下键选择你的系统盘回车进入。**括号带U盘型号或者品牌名的就是，比如Sandisk(闪迪)**，另外的叫“Windows EFI boot manager“的是windows的引导项，后面括号内的是固态品牌和型号名，比如Micron BALABALABALA就是镁光的固态，SAMSUNG BALABALABALA就是三星的

### 3.硬盘分区

1. 在Windows或者PE中用管理器/或者diskgenius 分出一个盘，建议100G.文件类型NTFS，不选择格式化     （好吧格式化了也没事）
2. EFI分区得重新建立，300M以上【见 5.EFI修改- EFI扩容&重建】
3. 最好不要留剩余空分区,尤其200M以下，用Diskgenius合并

### 4.U盘准备

* 格式化为FAT32

* 最好用Diskgenius分区**一个EFI区**，**一个镜像区**，**一个PE区**（为了防止未知的问题报错出现，非常建议在U盘装一个PE，包括后续給EFI分区添加或者修改文件，会遇到管理权限不够，或者不懂挂载分区，在用户Windows系统下分区位置不够直观等问题也会用到PE）

* 用balenaetcher 或者Rufus将镜像写入U盘

* 将文件名为“EFI”的文件夹放入U盘 的 EFI 分区，并替换掉原有的示范文件 **注意：如果你没在分出单独的EFI分区，直接粘贴过去，会提示要格式化此分区才能用，这样不可行**

* 关机进入下一步

### 5. EFI修改（因为是配制好的，基本不用动，直接复制efi到分区就好，这里只是介绍个别需要修改或屏蔽的配置文件）

- **EFI扩容&重建**：

  a. "磁盘管理"—点击空闲盘，压缩1G出来（输1024）

  b. win+R,输入cmd 或者 直接搜索cmd打开，

  `diskpart`

  `list disk //你的磁盘应该会显示disk 0`

  `select disk 0`

  `list partition`

  `create partition efi`

  `format quick fs = fat32`

  `assign letter = P`

  `list volume //	检查`

  `list partition //检查`

  `exit`

  退出重新打开cmd

  `bcboot C:\windows /s P: /f UEFI`

  `exit`

  重启

  用Diskgenius或者Minitool partition wizard这类分区工具把之前的100M EFI分区删了就好了

- [**OC(OpenCore)**](https://kkgithub.com/ic005k/OCAuxiliaryTools/releases/download/20240001/OCAT-Win64.zip "OC 修改工具")修改工具

- [**nootedred**](https://github.com/ChefKissInc/NootedRed "Nootedred")这个驱动让我们能在AMD驱动核显，不过最好别一开始就启用，先装好再回PE修改启用，禁用的话就是打开OCAT应用，点开EFI-OC-config.plist,打开界面找到Kernal，找到nootedred,取消打勾，**安装好之后得回来修改启用，不然很卡没法用**

- 网卡原装的是联发科MT7921，不支持，所以EFI支持的是英特尔AX210（211也可以），如果你的网卡没换，先修改下EFI：**用[OCAT](https://github.com/ic005k/OCAuxiliaryTools "OCAT下载链接页面")工具打开EFI-OC-config.plist文件，(注意文件名一定是config.plist,其他相似名称的文件是备份，怕修改错误备用的）然后点kernal,在一堆.kext文件结尾文件列表往下滑，将带"Intel"字样的文件取消打勾禁用**，这样防止驱动不了开机报错，暂时换不了网卡的话可以用拓展坞连网线或者手机插数据线网络共享，因为USB口是驱动了的。**！⚠️只有远离电源键那个USB口被驱动（第二个）**

  - 如果是R7版本的话就修改[**核心参数**](https://github.com/AMD-OSX/AMD_Vanilla "AMD核心参数") 为8核
    **⚠️！这里只是修改指南，提供的EFI配置好的不用修改，只有在升级新系统比如macOS Sequoia 才需要更新内核，并且修改核心参数**


### 6.安装系统&双系统引导

1. 在**3-BIOS设置-第四步**时，在点击install MacOS 后会进入一个安装界面，选择磁盘工具，找到你分好的那个盘，文件类型选APFS，不要选下面加密的，然后格式化

2. **注意**此时只是系统文件安装在了分好的文件盘，EFI文件是要单独在EFI的分区，此时我们还是通过U盘的EFI引导启动的，拔掉U盘开机就还是默认进入Windows

3. 重启到PE，将U盘EFI分区的OC文件夹复制到EFI分区根目录>打开**EasyUEFI**（自己搜下载）--->选择文件夹📁添加新的引导——>选择电脑磁盘的EFI分区-- OC—openefi文件；**记得备注个名字叫OC，然后添加完成**，之后就把这个OC引导**上移到顶部**

4. 重启就生效了

   ### 7.开启HIDPI

接下来还会遇到的一个问题就是nootedred驱动只能够实现1280x800这个分辨率的HIDPI，其他的分辨率下屏幕都感觉很模糊。所以我们还需要用到一个HIDPI的工具，实现我们屏幕在指定分辨率的高清晰度。

进入MacOs,打开终端

在终端中输入以下的命令 “分辨率是可以手动输入的，后面选Manual就自定义了，只要比例对都可以，1600*1000 或者1100都可以（感觉1000很合适）"设备型号记得选Macbook Pro"

`sh -c "$(curl -fsSL https://html.sqlsec.com/hidpi.sh)"`

#**Bug 提示**;如果你在安装界面发现卡住了，看看有没有插鼠标键盘的**接收器、外设 拔掉！**（超大声！），可能会无法正常驱动而卡住安装界面；尤其是不小心插在没驱动的I/O接口，多半是导致卡logo的原因

## ！一些提示

* 这个文件是依据小新Pro16锐龙版R5-5600H配置好的，硬件详细配置为CPU：R5-5600H，固态：镁光，网卡：原装联发科，得换成英特尔AX210或者想要Airdrop,handoff,sidecar随行换博通网卡（网卡配置也得换）

* 其他品牌的话，大部分硬件相似理论上修改好其他硬件配置应该也是可以用的

* 发现哪些功能用不了的话直接从新下最新版本，将.kext文件拖到Kext文件夹，然后替换就算更新完成了

  [**麦克风声卡驱动**](https://github.com/qhuyduong/AppleALC "麦克风声卡驱动")
  
  [**wifi**](https://github.com/OpenIntelWireless/itlwm/issues/883 "Wifi驱动")“在issue最下面”

  [**蓝牙**](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases "蓝牙驱动")

  还需要一个[**bluetoothFixup驱动**](https://github.com/zxystd/BrcmPatchRAM  "BluetoothFixup驱动")
  **取出文件夹中"BluetoothFixup"** 这个文件就好

  **除了Montery高版本的MacOS都不需要“Bluetoothinjector”**,可以取消打勾，开机明显快很多

* 【开机 跑码 or 】在OCAT打开config.plist后，NVMEM—add--7C那一栏--boot-args,在Value(值)一栏最后空格加“ -v” 就能在启动界面看到跑码，有报错可以开启，方便诊断，后续成功可以去掉，只显示进度条，更美观

  

🎉 虽然不是什么恨技术活，就是个操作指南，还是总结了自己不少亲身经验在里面的，为了小白友好考虑了不少。撰写不易，还望顺手点赞👍关注支持下B站 **@利头霍斯**就是支持了

