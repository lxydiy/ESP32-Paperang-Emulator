# ESP32 Paperang Emulator

#### 用ESP32 DIY 热敏打印机，模拟Paperang/喵喵机通信协议，可以通过蓝牙连接手机，接收、打印Paperang APP/喵喵机APP/作业帮APP发送的打印数据

## 提示  2021-8-28
#### 这个版本过于不完善，我也在尝试制作2.0版，但是因为学业原因可能需要*很久*，所以推荐两个优秀的二次开发项目，如果资金充足可以考虑：  
[Mao喵喵机热敏打印机--樱猫](https://www.oshwhub.com/SakuraNeko/mao-re-min-da-yin-ji)  
[喵喵机-热敏打印机-改--freedom789](https://www.oshwhub.com/freedom789/print-fpc1)  
因为我比较菜，又要考虑兼容性问题，所以就没有画fpc，这个项目就当是模板吧。这里给各位二次开发者一些建议：  
电池最好更换为2节串联，不要用原版升压芯片，或者电池加超级电容，不然带不动；  
电机驱动可以考虑a4988驱动芯片，不要采用恒压驱动的方式，那样的话电机会非常热；  
原版电路mos管驱动可能有问题，但是能用，这个保护电路不可以省略，不然上电esp32某些引脚默认会上拉或下拉，stb就会激活，烧出一条黑线；  
esp32不需要wrover，根据我的计算60k缓冲区足够打印1m长，把ps_malloc改成malloc就行。  
欢迎大家二次开发并开源，只要注明一下原作者就可以。  
下面是正文  

---
![封面](https://github.com/1452206376/ESP32-Paperang-Emulator/blob/master/images/main.png )

[Bilibili视频链接](https://www.bilibili.com/video/BV1v54y127xZ )

-----

** 3D模型可能不适合你的打印头,如果打印头尺寸与3D模型相差太大建议重新设计3D模型 **

## 注意事项/调试过程

** 注意：以下内容必须认真阅读，否则可能会出现各种问题，甚至烧坏打印头 **

PCB上的SDA接打印头的SI

已经确认STB5 STB6不能作为输出引脚，ADC和KEY没用到，可以按照视频说明接到那两个引脚上

如果下载程序时不能自动复位可以在ESP32的EN脚对地并联一个20uf以上的电容

如果觉的好别忘了Star :)

### 焊接

焊接时按照元器件从小到大顺序焊接，焊接完成后先检查是否有短路、虚焊、漏焊元件等情况（可以不焊接CP2104和自动复位电路，PCB后面预留有烧录程序用的测试点）

PCB焊接完成后不要立刻焊接打印头测试，先连接电池和开关，将电位器指针调至中间，闭合开关，用万用表直流50V或20V档位测量 VH+ 与 GND 之间的电压，并**缓慢调节**电位器，逆时针旋转升压，顺时针旋转降压。直到输出电压略小于打印头加热元件额定电压，然后焊接打印头。VH+ 可调节的范围：VBAT - 28V，最好不要将电位器调到两端。

我使用的打印头的额定电压为7.2V，型号为三星SMP640, 可作参考

------

将libraries文件夹与esp32开发板安装目录下的libraries文件夹合并，这个文件夹的位置：

Windows，一般在

`C:\Users\Admin\AppData\Local\Arduino15\packages\esp32\hardware\esp32\1.0.4\libraries`

Linux，一般在

`~/.arduino15/packages/esp32/hardware/esp32/1.0.4/libraries`

修改了官方库的缓冲区大小
-----

### 编译/连接/上传

开发板选择ESP32_Wrover_Module，认真核对Printer.ino开头部分的的配置是否符合打印头，然后上传。

上传完成后，如果听到蜂鸣器响1短声，说明启动正常，且 ESP32 的 PSRAM 工作正常。

### 打印测试

在APP中连接打印机，打印**一行**文字，如果打印过程中打印机中途**停止转动**、打印**速度过慢**、文字**颜色过深**或像素点形状**不规则**，请立刻（2秒内）断开开关检查**程序**配置、**电路**连接、打印纸上的**图案**和**串口**输出信息中是否有“ERROR”字样（有就说明有丢包现象），这些信息有助于排错。因为ESP32复位后需要一段时间自检，然后配置IO口。如果开关断开不及时很容易烧坏打印头

默认打印头步进电机转**4**步，打印机走纸**一像素**的距离，如果发现打印的文字长度**过长**或**过扁**，请修改startPrint函数中的goFront1()函数出现位置和次数，这个函数的作用是使打印头步进电机走1步

如果打印正常，就可以把它固定进3D打印的盒子里了

最后，祝大家一次成功（上面说的错误很容易修复，基本都是丢包或配置不当造成的），没有信心可以准备两个型号相同的打印头，用其中一个测试好后换另外一个打印头装盒，这样可以保证打印机做好后打印效果最好。

有什么解决不了的问题或 代码/PCB 的bug请发Issue或PR。建议直接私信作者Bilibili @小李电子实验室  UID:213123506，开学后每三周检查一次私信和评论

-----
## 文件列表：
|文件夹名|描述|
|--|--|
|3D-Model|3D模型|
|images|一些图片|
|libraries|库文件|
|PCB|PCB工程文件和GERBER文件|
|Program|Arduino 程序文件|
|Python|（仅作备份）Python程序|
-----

# 打印头资料

## 一些已找到资料的打印头，到资料来源处直接搜索型号就行

|打印头型号|资料来源|
|:-:|:-:|
|JX-700-48R|百度文库|
|JX-700-02|百度文库|
|SMP640|这个repo的images目录,只有引脚定义|

目前就找到这些比较常见的，淘宝上综合排的靠前，价格<20元的。实在不行到官网上看看没准也可以找到，欢迎提交PR补充

第一脚可以参照电机位置，写着类似A+ A- B+ B- 或AP AM BP BM AN BN之类的引脚一般在电机一侧

-----

这个3D模型可以装下长小于68mm的打印头机芯，和PCB的长相同，小一点没关系。打印头机芯高不能超过30mm，否则请重新设计外壳3D模型，不是特别难，我用的工具是Tkinkercad，直接搜就行

## 在找资料过程中的一些发现

中崎AB-58KII、芯烨XP-58IIIA、精芯JX-700-48R引脚数相同，排线外观相似（前两个的排线一模一样，连分叉的位置都一样，第三个的排线上也就多了两条引出空脚的铜箔，前两个是悬空）

大多数打印头的加热电压(VH)为7.2V，峰值为8.5V

我搜到的所有<20元的57mm热敏打印头都是SPI接口，一个数据锁存控制引脚和六个Strobe引脚（用于选通部分打印头），分辨率每行384点

# 物料清单

|名称|数量|备注|
|:-:|:-:|:-:|
|热敏打印头(机芯)|1|先找资料后购买|
|ESP32-Wrover|1|是Wrover系列就行，否则焊不上去|
|Type-C接口|1||
|贴片蜂鸣器 封装：5020|1|可选|
|TC1508S芯片|1||
|SX1308芯片|3|防止调过头，多买两个|
|CP2104 QFN24|1|可选，USB转串口芯片，不会拖焊QFN可以买焊锡膏吹焊，PCB如果没订钢网不建议买瓶装的，太干不好用。拖焊技巧：一大坨助焊剂，干它就完了|
|3362电位器 100K|1||
|AON6512 N-MOS QFN-8|1|或者一坨锡连接DS极，这里的mos感觉没什么用，可能是电路有问题，拆了直接连起来不影响使用|
|XC6206P332 (或者662k) SOT23-3|1|3.3V LDO|
|SS8050 SOT23-3|10|可能用不了这么多，但是一般都是几十个一起卖。SS8050>S8050，可以直接代换|
|10k电阻 0603|一组(不是64)|一般至少卖50个|
|200欧姆电阻/200R 0603|一组|同上|
|LTC4054 SOT23-5|1|锂电池充电管理芯片|
|LED 0603|>5|颜色随意，多买几个防止焊坏|
|1N5822|1||
|2.2K电阻 0603|>2||
|1UF电容 0603|一组||
|0805 电容|1|绿波用，大亿点没关系，耐压>=10V|
|锂电池+保护板|1|可以是18650，刚好放得下。最好用动力电池|
|船形开关 10x15mm |1|用别的开关也行。我的这个模型开关孔和USB孔要自己看好PCB安装位置切，或者改模型。一是因为每个人的电池和打印头大小可能不同，开孔位置不固定，二是3D打印机直接打印容易凹陷，我又不想加支撑|
|...|...|应该没什么了，LED限流电阻都用200欧没问题，0603电容全部用1uf|

## 参考资料链接

[喵喵机折腾记-ihcblog](https://www.ihcblog.com/miaomiaoji/ )

[hansel163-paperang_printer](https://github.com/hansel163/paperang_printer )

先整理这么多吧
