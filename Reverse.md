# 1.手脱 UPX 壳

# 调试工具是 ExeinfoPE 和 OD

## 方法1：单步跟踪
不能让它实现向上的跳转, 向下的跳转就让它实现.
遇到向上的跳转, 在这个跳转的下一条指令处右键找到 "断点"→"运行到所选位置F4", 
或者在下一条指令处打个断点, 然后再运行程序, 程序就停在断点, 这时我们再取消这个断点, 这样就跳过了这个向上的跳转.
OD 会在汇编代码的左边用箭头的形式来告诉你是向上还是向下跳转, 红线代表跳转已经实现. 绿线代表跳转没有实现.
如果那个向上跳转的下一条指令还是跳转, call 也算跳转, 那么我们一般是隔一行调试.
一般来说, 一个很大跨度的跳转就直接跳到 OEP 了.
脱壳方法 :
### 1. OD 插件 OllyDump
- 方式一
- 方式二
### 2. LordPE
先修正一下镜像大小, 然后完整转存
发现报错, 然后查壳发现没有脱干净
这时候我们用到 ImportREC 工具
首先在上面找到那个程序, 因为在 OD 载入之后, 那个程序已经自动载入到进程里面了
然后在下面把程序如果地址改成真正的 OEP, 也就是之前让大家记住的 7738C (OllyDump中看到的)
自动查找
获取输入表
显示一下无效的函数
然后我们修复转存文件, 选中刚才转存的文件

## 方法2：ESP定律法
https://www.cnblogs.com/Fightingbirds/p/3172897.html
ESP 是一个寄存器
只有 ESP 突变变红, 或者关键句的下一个
然后我们在 ESP 的这个值, 右键点 "在数据窗口跟随"
或者可以这么操作 :
dd XXXXX
hr XXXXX
接着我们在数值上, 左键, 然后右键找到 "断点", 然后 "设置硬件访问断点", 然后选择 "word", 其实 "word" 和 "dword" 都一样
然后运行, 记得把硬件断点删除, 免得出现一些问题

## 方法3：2次内存镜像法
https://blog.csdn.net/nhgxxyy198990/article/details/79885798
https://ctf-wiki.github.io/ctf-wiki/reverse/windows/unpack/memory/
所谓内存镜像, 当然我们得先去找这个内存, 怎么找内存, 我们点 OD 上面的 "查看", 然后点这个 "内存", 或者点上面字母里面的 "M", 当然你也可以直接用快捷键
我们找什么呢, 我们找程序段的第一个 .rsrc, 系统的领空我们不用管它, 然后我们下一个断点 F2, 然后运行
接着再找一次内存, 找这个 00401000, 然后下断点运行

## 方法4：一步直达法
一般大部分的 UPX 和 Aspack 壳才能用
一开头就是一个 `pushad`, 把所有寄存器数据都压入栈中
有进必有出
右键最开始的 `pushad`, 然后 "查找", 然后 "命令", 输入 `popad`
下面的那个 "整个段块" 一般不勾选, 因为程序有很多段, 每个段都是入栈出栈很多, 勾了之后我们就不知道哪个是哪个了
然后查找, 我们可以先下断点然后运行再断点, 或者直接 F4 运行到所选位置, 然后接着单步, 就直接到达 OEP 了.

# 2.手脱 ASPACK 壳

# ASPack 2.12 -> Alexey Solodovnikov 工具：PEID和OD

## 方法1：单步跟踪

首先我们单步, 然后发现程序运行起来了
也就是我们所说的 "跑飞了"
这时候我们应该用 F7 跟进去, 看看 call 里面具体有什么内容
一直单步, 然后找到了 popad, 说明离真正的 OEP 不远了
然后单步发现有个跳转, 然后我们就跳转到了 OEP

## 方法2：ESP定律

ESP 定律就是在关键句的下一句 ESP 处下断.

## 方法3：一步直达

适合大部分的 UPX 壳和 ASPACK 壳
查找命令 `popad`
发现不是, 于是我们接着查找
或者直接用下个 Ctrl+L
找到之后我们可以直接 F4
然后我们就跳到 OEP

## 方法4：2次内存镜像

Alt M 找程序段第一个 .rsrc

## 方法5：模拟跟踪

这个方法和方法 6 的共同点是都是让 OD 自动去查找程序的 OEP
找到内存 M, 
找到包含 SFX, imports 的这一行
然后命令行直接输入 :
tc eip<xxxxxx (地址)
然后发现 OD 左上角显示 "跟踪", 这说明 OD 已经在自动跟踪了, 等一会就自动跳到 OEP 了

## 方法6：SFX 模拟跟踪

"选项">"调试设置">"SFX"
然后我们选择 "块方式跟踪", 就是第二个, 当然有的要选第三个, 然后点确定, 重载一下
然后 OD 自动跳到了 OEP

### 补充 : [SFX 法](https://ctf-wiki.github.io/ctf-wiki/reverse/windows/unpack/sfx/)

### 一般情况, 还是建议手动查找 OEP, 如果你熟练了也可以用自动的方法.
