title: 你不知道的终端Terminl

date: 2014-11-04 09:57:11
tags:
categories:

---

以下内容大部分源自 __`man console_codes`__

###关于转移字符

首先废话下转移字符，因为后面介绍到CSI等部分时会涉及到.

  1. 八进制: "\" + 1~3位的8位数字 例如 "\123";
  2. 16进制长 "\u" + 4位16位数字  例如 "\u738b" (王).
    数字即 `char.charCodeAt(0).toString(16)`的值
  3. 16进制短 "\x" + 两位16位数字 例如 "\x55"(U) 它等价于 "\u0055";
  4. 其它诸如`\t`等制表符 等就不再做说明了

###终端的control codes

比如Linux的终端实现了VT102([?](http://en.wikipedia.org/wiki/VT100))和ECMA-48的，以及一些私有字符集。

看到VT102 terminal实体可能你就明白为何我们称之为“终端”. 对，就是这大家伙
![VT102](http://terminals.classiccmp.org/wiki/images/thumb/d/d0/DEC_VT102_291060983768-1.jpg/800px-DEC_VT102_291060983768-1.jpg)

VT102 被称之为 De facto standard, 即虽然没有相关标准化，但是已然成为了一种事实标准，
我们常规Linux的终端就是模拟实现了它的接口标准


当你输入字符时，如果终端判断其为一个控制字符或正处于一个，它不会将其转换为字符实体并显示，
而是会发生一些特殊的处理，比如改变字体颜色，上下移动光标隐藏光标，发出蜂鸣声等等。


##控制字符 (Control characters) 与转移序列

有以下控制字符
- 00(NUL)
- 07(BEL)
- 08(BS)
- 09(HT)
- 0a(LF)
- 0b(VT)
- 0c(FF)
- 0d(CR)
- 0e(SO)
- 0f(SI)
- 18(CAN)
- 1a(SUB)
- __1b(ESC)__
- 7f(DEL)

事实上我们平时term-dom中使用到的只有ESC转义起始符(以及用来蜂鸣的`\x07` BEL);

控制字符会立即生效并且不会被显示, 如果前面的序列正处于CSI阶段，CSI会在生效后继续进行,
唯一的例外是ESC控制符, 它会终止当前的CSI序列


### ESC

ESC控制符是我们最常用的

ESC c	RIS	重绘屏幕.
ESC D	IND	换行.
ESC E	NEL	新的一行.
ESC H	HTS	设置当前列为制表位.
ESC M	RI	翻转换行(Reverse linefeed).
ESC Z	DECID	DEC 私有定义.内核将其解释为
		VT102字符,返回字符ESC [ ? 6 c.
ESC 7	DECSC	存储当前状态(光标坐标,
		属性,字符集).
ESC 8	DECRC	恢复上一次储存的设置
ESC [	CSI	控制序列介绍
ESC %		开始一个字符集选择序列
ESC % @		\0\0\0选择默认字符集(ISO 646 / ISO 8859-1)
ESC % G		\0\0\0选择 UTF-8
ESC % 8		\0\0\0选择 UTF-8(已不用)
ESC # 8	DECALN	DEC 屏幕校准测试 - 以E's填充屏幕.
ESC(		开始一个 G0 字符集定义序列
ESC( B		\0\0\0选择默认字符集(ISO 8859-1 mapping)
ESC( 0		\0\0\0选择 vt100 图形映射
ESC( U		\0\0\0选择空映射 - 直接访问字符ROM
ESC( K		\0\0\0选择用户映射 -  由程序\fBmapscrn\fP(8)
		\0\0\0加载.
ESC )		开始一个 G1 字符集定义
		(后面跟 B,0,U,K,同上).
ESC >	DECPNM	设置数字小键盘模式
ESC =	DECPAM	设置程序键盘模式
ESC ]	OSC	(是perating system command的缩写)
ESC ] P Inrrggbb P: 设置调色板,后面紧跟7个
十六进制数,再跟一个 P :-(.
这里 \fIn\fP 是颜色(0-16),而 \fIrrggbb\fP 表示
红/绿/蓝 值(0-255).
ESC ] R: 重置调色板

这里的部分序列是term-dom中需要的


### CSI

CSI(`ESC [`) 是由分号隔开的十进制数.空参数或缺少的参数以0处理.
可以用一个问号代替参数序列.

CSI可以实现一些文字效果，比如term-dom大部分实现都基于CSI来实现文件颜色，背景色等

CSI序列的动作由最后一个数字决定

那么问题来了，我只是想换个文字颜色你和我扯那么多干嘛？











1
