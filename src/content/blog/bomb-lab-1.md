---
title: "Bomb Lab (1/2)"
description: "Bomb Lab 的作业报告（第一部分）"
pubDate: "Aug 8 2024"
heroImage: "https://csapp.cs.cmu.edu/3e/images/csapp3e-cover.jpg"
tags: ["CS:APP"]
---

本文是我个人的 Bomb Lab 作业报告，涉及内容：汇编语言命令，反汇编；GDB 调试器的使用

## 准备工作

关于题目的介绍可以见 [Writeup file](https://csapp.cs.cmu.edu/3e/bomblab.pdf). 在[官方网站](https://csapp.cs.cmu.edu/3e/labs.html)下载源代码材料之后解压压缩包：

```bash
tar -xvf bomb.tar
```

解压后文件目录如下所示：

```
bomb/ 
├── bomb 
├── bomb.c
└── README
```

首先观察 `bomb.c` 文件中，在每一个阶段中，读取输入随后经过名为 `phase_[NUM]` 的函数之后，再执行 `phase_defuse()` 函数。引爆炸弹的函数部分被包装起来了，因此接下来我们要一一分析六个阶段的 `phase_[NUM]` 函数。

根据题目要求，我们创建一个名为 `psol.txt` 的输入，随后每一个部分的答案都将会被记录在该文件里。

在接下来的阶段中，我们使用 GDB 调试器，输入以下指令进入 GDB 调试器 TUI 界面：

```bash
gdb -tui bomb
```

## Phase 1

对 `phase_1` 函数进行反汇编：

```bash
(gdb) disas phase_1
```

> `disas` 是 `disassemble` 的缩写。

输出如下所示，并进行分析：

```text
Dump of assembler code for function phase_1:
   0x0000000000400ee0 <+0>:     sub    $0x8,%rsp
   0x0000000000400ee4 <+4>:     mov    $0x402400,%esi
   0x0000000000400ee9 <+9>:     callq  0x401338 <strings_not_equal>
   0x0000000000400eee <+14>:    test   %eax,%eax
   0x0000000000400ef0 <+16>:    je     0x400ef7 <phase_1+23>
   0x0000000000400ef2 <+18>:    callq  0x40143a <explode_bomb>
   0x0000000000400ef7 <+23>:    add    $0x8,%rsp
   0x0000000000400efb <+27>:    retq
End of assembler dump.
```

1. 从名字上看，引爆炸弹的函数是由 `<explode_bomb>`  造成的。
2. 根据 <+14>, <+16> 处可以判断，`<strings_not_equal>` 的返回值必须是非零才可以跳过 <+18> 的引爆。
3. 因此推测输入必须是一个字符串，且必须与 <+4> 处的字符串内容相同。

随机在输入文件中的第一行输入一串字符串，下一步就是运行代码。

> 输入 `layout asm` 进入反汇编指令视图。

首先设置断点：

```bash
(gdb) b phase_1
(gdb) b explode_bomb
(gdb) r psol.txt
```

> 可能需要输入 `refresh` 刷新一下 TUI 视图。

 输入 `si` 以逐步执行指令，接下来重复执行 `si` 命令直到下一个断点（或者 <+14> 处），当进入 `<strings_not_equal>` 内部时输入 `finish` 以直接结束运行。
 
> `si` 允许我们逐指令运行，且只用按 RETURN 即可重复上一个命令。后面我们会重复这个操作。

访问 `%esi` 寄存器内内容，或是直接输出 `$0x402400` 的内容：

```bash
(gdb) x/s $esi
(gdb) x/s 0x402400
```

得到输出：

```text
0x402400:       "Border relations with Canada have never been better."
```

将该内容复制到我们的输入文件中并插入新的空行后，并重新运行程序。

> 如果不插入新的空行，那么炸弹就要被引爆了。详细看文档提示：程序会持续读取直到遇到 EOF 标识。因此如果没有 EOF（在这里是 RETURN）那么就会出问题。

在中间可以检验 `%eax` 寄存器的值，`(gdb) p $eax` 得到 `$1 = 0` 的输出，因此我们的输入正确。

> 并不需要退出 GDB 调试器就可以重新运行程序：保存 `psol.txt` 修改后的数据之后，运行 `(gdb) r psol.txt` 即可。
## Phase 2

反汇编后的输出是：

```bash
Dump of assembler code for function phase_2:
   0x0000000000400efc <+0>:     push   %rbp
   0x0000000000400efd <+1>:     push   %rbx
   0x0000000000400efe <+2>:     sub    $0x28,%rsp
   0x0000000000400f02 <+6>:     mov    %rsp,%rsi
   0x0000000000400f05 <+9>:     callq  0x40145c <read_six_numbers>
   0x0000000000400f0a <+14>:    cmpl   $0x1,(%rsp)
   0x0000000000400f0e <+18>:    je     0x400f30 <phase_2+52>
   0x0000000000400f10 <+20>:    callq  0x40143a <explode_bomb>
   0x0000000000400f15 <+25>:    jmp    0x400f30 <phase_2+52>
   0x0000000000400f17 <+27>:    mov    -0x4(%rbx),%eax
   0x0000000000400f1a <+30>:    add    %eax,%eax
   0x0000000000400f1c <+32>:    cmp    %eax,(%rbx)
   0x0000000000400f1e <+34>:    je     0x400f25 <phase_2+41>
   0x0000000000400f20 <+36>:    callq  0x40143a <explode_bomb>
   0x0000000000400f25 <+41>:    add    $0x4,%rbx
   0x0000000000400f29 <+45>:    cmp    %rbp,%rbx
   0x0000000000400f2c <+48>:    jne    0x400f17 <phase_2+27>
   0x0000000000400f2e <+50>:    jmp    0x400f3c <phase_2+64>
   0x0000000000400f30 <+52>:    lea    0x4(%rsp),%rbx
   0x0000000000400f35 <+57>:    lea    0x18(%rsp),%rbp
   0x0000000000400f3a <+62>:    jmp    0x400f17 <phase_2+27>
   0x0000000000400f3c <+64>:    add    $0x28,%rsp
   0x0000000000400f40 <+68>:    pop    %rbx
   0x0000000000400f41 <+69>:    pop    %rbp
   0x0000000000400f42 <+70>:    retq   
End of assembler dump.
```

首先可以看到的是 `<read_six_numbers>` 直接推测我们需要输入六个数字，我们随机输入。

> 我选择了 111, 222, 333, 444, 555, 666 这样具有高辨识度的数字，便于我后面发现某个寄存器存储的是我的输入的数据。

观察可以发现指令中含有很多跳转指令，在给 `<explode_bomb>` 设置断点之后，我们可以大胆地逐指令运行这一部分指令集。

当代码来到 <+14> 行，发现进行了一次比较，且 <+18>, <+20> 要求两者需要相等不然引爆炸弹。`$0x1` 显然就是 1，我们输出 `%rsp` 寄存器中的数据：

```bash
(gdb) p *(int *)$rsp
```

> 解释一下，由于我们知道输入的数据是整型，此处读取的是 `%rsp` 寄存器存储的地址所执行的值，因此需要强制指定两部分：解引用 + 指向一个整型的指针。

发现正好是我们的第一个输入，因此要求第一个数是 1.

接下来运行到 <+32> 部分亦是同理，运用相同的方式检查 `$eax` 和 `(%rbx)` 的数据我们发现第二个输入要求是 2. 同理，第三个数是 4，第四个数是 8，...

最终本阶段的答案应是 `1 2 4 8 16 32`.

> 检查 `%eax` 至少有两种方法：`p $eax` 以及持续跟踪其值 `display $eax`. 由于持续访问两个寄存器的值，建议都使用 `display` 替代 `p`. 可以输入 `undisplay` 停止跟踪其值。

当然除了自动运行程序之外，我们可以手动分析：很明显观察到以上指令中包含了一个循环。

根据 `%rsp` 寄存器结构特性，在 <+2> 处来到其栈底，后续所有步骤都是向上爬升。

> `%rsp` 是堆栈指针寄存器，遵循 LIFO. `%rsp` 既可以管理函数之间的调用，也可以作为局部变量存储。

栈底的数应该是 `0x1` 即 1，否则在第 2 步中，按序执行会直接引爆炸弹。随后第 3 步跳转至 <+52> 行。

从 <+52> 到 <+27>，寄存器 `%eax` 的值与 `%rbx-4` （即 `rsp` 栈底，也就是上面推导出来的 1）相同，因为在 <+52> 中和 <+27> 执行之后其偏移值相互抵消了。随后在 <+32> 中得出结论：栈底的上面一位是 1 + 1 = 2.

后面的过程类同。随后，来到 <+41>，执行到 <+48> 之后跳转到 <+27>... 流程图可以如下所示，直到 `rbx` 递增至栈顶时推出循环（见 <+41> 行）：


![](https://pub-f4fb14aad5ef4ee6a83bd71292941254.r2.dev/%E6%9C%AA%E5%91%BD%E5%90%8D%E7%BB%98%E5%9B%BE.drawio%20(2).png)

循环转化为 C 语句写法即为[^2]：

```c
// int rsp[6], rbx = rsp+1, rbp = rsp+6; int eax;
while (rbp != rbx)
{
	eax = *(rbx-1);
	eax = eax + eax;
	if (eax == *rbx) 
		continue;
	else 
		explode_bomb();
}
```

## Phase 3

反汇编结果：

```text
Dump of assembler code for function phase_3:
   0x0000000000400f43 <+0>:     sub    $0x18,%rsp
   0x0000000000400f47 <+4>:     lea    0xc(%rsp),%rcx
   0x0000000000400f4c <+9>:     lea    0x8(%rsp),%rdx
   0x0000000000400f51 <+14>:    mov    $0x4025cf,%esi
   0x0000000000400f56 <+19>:    mov    $0x0,%eax
   0x0000000000400f5b <+24>:    callq  0x400bf0 <__isoc99_sscanf@plt>
   0x0000000000400f60 <+29>:    cmp    $0x1,%eax
   0x0000000000400f63 <+32>:    jg     0x400f6a <phase_3+39>
   0x0000000000400f65 <+34>:    callq  0x40143a <explode_bomb>
   0x0000000000400f6a <+39>:    cmpl   $0x7,0x8(%rsp)
   0x0000000000400f6f <+44>:    ja     0x400fad <phase_3+106>
   0x0000000000400f71 <+46>:    mov    0x8(%rsp),%eax
   0x0000000000400f75 <+50>:    jmpq   *0x402470(,%rax,8)
   0x0000000000400f7c <+57>:    mov    $0xcf,%eax
   0x0000000000400f81 <+62>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f83 <+64>:    mov    $0x2c3,%eax
   0x0000000000400f88 <+69>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f8a <+71>:    mov    $0x100,%eax
   0x0000000000400f8f <+76>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f91 <+78>:    mov    $0x185,%eax
   0x0000000000400f96 <+83>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f98 <+85>:    mov    $0xce,%eax
   0x0000000000400f9d <+90>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f9f <+92>:    mov    $0x2aa,%eax
   0x0000000000400fa4 <+97>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400fa6 <+99>:    mov    $0x147,%eax
   0x0000000000400fab <+104>:   jmp    0x400fbe <phase_3+123>
   0x0000000000400fad <+106>:   callq  0x40143a <explode_bomb>
   0x0000000000400fb2 <+111>:   mov    $0x0,%eax
   0x0000000000400fb7 <+116>:   jmp    0x400fbe <phase_3+123>
   0x0000000000400fb9 <+118>:   mov    $0x137,%eax
   0x0000000000400fbe <+123>:   cmp    0xc(%rsp),%eax
   0x0000000000400fc2 <+127>:   je     0x400fc9 <phase_3+134>
   0x0000000000400fc4 <+129>:   callq  0x40143a <explode_bomb>
   0x0000000000400fc9 <+134>:   add    $0x18,%rsp
   0x0000000000400fcd <+138>:   retq   
End of assembler dump.
```

第一眼感觉 <+50> 到 <+118> 是一个 `switch-case` 语句，不过我们首先先研究前面的部分。

这道题无法直接判断输入数据的个数和格式了，这里我直接复制了第二行的答案作为测试。逐指令运行之后，程序来到 <+24> 进行在 C99 标准 `sscanf` 函数的调用，随后在 <+29> 进行比较，要求函数的返回值大于 1 不然引爆炸弹。

阅读 `sscanf` 函数文档中关于其返回值的记载可以发现，其返回值是匹配的（有效的）输入个数。因此我们输入的数字至少需要大于 1：

```text
On  success, these functions return the number of input items successfully matched and assigned; this can be fewer than provided for, or even zero, in the event of an early matching failure.
```

> 在命令行中输入 `man sscanf` 即可阅读相关内容。

此外通过 `p $eax` 检查发现返回值是 2，符合要求。 这也意味着题目只要求两个输入，因此只保留我们目前的前两个输入。

在 <+39> 行处，若 `rsp+8` 的值大于 7 则会跳转至 <+106> 处即爆炸。检查该位置的变量：

```bash
(gdb) p *(int *)($rsp + 0x8)
```

正好是我们的第一个输入，因此我们的第一个输入不能大于 7. 目前的输入符合要求。

逐指令运行之后来到 <+118> 和 <+123> 处，要求 `(%rsp + 0xc)` 和 `%eax = $0x137` 相等不然引爆炸弹。易发现第一个数即是我们的第一个输入，将 `$0x137` 转化成十进制数即为 311，因此第二个输入应当只能是 311（当第一个输入是 1 时）。

> 其实在 <+4>, <+9> 处函数调用时就偷偷告诉你了，首先读取的是第二个输入，存储在 `0xc(%rsp)` 中，第一个输入是在其下方 `0x8(%rsp)`. 

因此本题其中一个答案是 `1 311`.

> 正如在开头说的，下面一部分是一个 switch 结构，根据第一个输入而定。可以尝试着为第一个数输入不同的值（主要要是无符号数且小于 7）会得到不同的第二个值。






[^2]: [Analysis of Bomblab by Xu Yichang](http://home.ustc.edu.cn/~xuyichang/blogs/bomblab.html)