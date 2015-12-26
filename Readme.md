
<script src="http://code.jquery.com/jquery-1.7.2.min.js"></script>
<script src="http://yandex.st/highlightjs/6.2/highlight.min.js"></script>
 
<script>hljs.initHighlightingOnLoad();</script>
<script type="text/javascript">
 $(document).ready(function(){
 	  var s = "<ul>";
      $("h1,h2,h3,h4,h5,h6").each(function(i,item){
        var tag = $(item).get(0).localName;
        
        
        $(item).attr("id","wow"+i);
        if (tag=="h1") {
        	if (s!="<ul>") {s+="</ul></li>";}
        	s+='<li><h4><a class="new'+tag+'" href="#wow'+i+'">'+$(this).text()+'</a></h4><ul>'
        }
        if (tag=="h2") {
        	s+='<li><a class="new'+tag+'" href="#wow'+i+'">'+$(this).text()+'</a></li>'
        }
       
        $(".newh1").css("margin-left",0);
        $(".newh2").css("margin-left",0);
      });
      s+="</ul>"
     console.log(s);
     $("#category").empty();
     $("#category").append(s);
 });
</script>
<div id="category"></div>

#第一章 嵌入式系统基础知识

##嵌入式系统的定义

+ IEEE：嵌入式系统是“控制、监视或者辅助设备、机器和车间运行的装置”

+ 普遍认同：以应用为中心，以计算机技术为基础，软硬件可裁剪，适应应用系统对功能、可靠性、成本、体积、功耗严格要求的专用计算机系统。

##嵌入式系统的组成

嵌入式系统一般由**嵌入式微处理器、外围硬件设备、嵌入式操作系统以及用户应用程序**4个部分组成。

##RISC与CISC的概念及特点

+ 复杂指令集计算机(Complex Instruction Set Computer,CISC)  
	指令由汇编语言所组成，把一些原来由软件实现的常用的功能改用硬件的指令系统实现，编程者的工作因而减少许多，在每个指令期同时处理一些低阶的操作或运算，以提高计算机的执行速度，这种系统就被称为复杂指令系统。
+ 精简指令集计算机(Reduced Instruction Set Computer,RISC)
	+ 具有大量的寄存器；
	+ 绝大多数操作在寄存器中进行，通过Load/Store指令在内存和寄存器间传递数据；
	+ 寻址方式简单；
	+ 采用固定长度的指令格式。
	+ 采用高级语言编程，重视编译优化工作，以减少程序执行时间。

##流水线技术的基本概念及ARM9采用的5级流水线

流水线是指在一个指令周期内处理器进行多种行为的处理，以增加处理器指令流的速度。

其基本思想是将一个重复的时许分解成若干个子过程，而每一个子过程都可以有效地在其专用功能段上与其他子过程同时执行。

+ ARM7 : 3级：取指，译码，执行

+ ARM9 : 5级：**取指，译码，执行，缓冲／数据，回写**

	+ 取指：指令从存储器中取出，放入指令流水线。
	+ 译码：指令译码从寄存器堆中读取寄存器操作数。在寄存器堆中有3个操作数读端口因此大多数ARM指令能在一个周期内读取其操作数。
	+ 执行：把一个操作数移位，产生ALU的结果。如果指令是Load或Store，在ALU中计算存储器地址。
	+ 缓冲/数据：如果需要则访问数据存储器；否则，ALU的结果只是简单地缓冲一个时钟周期，以便使所有指令具有同样的流水线流程。
	+ 回写：将指令产生的结果回写到寄存器堆，包括任何从存储器读取的数据。

#第二章 ARM体系结构
##寄存器结构
ARM微处理器共有**37**个寄存器，被分成若干个组，包括：

+ **31个通用寄存器**，包括程序计数器，均为32位寄存器。
+ **6个状态寄存器**，用于标示CPU的工作状态及程序的运行状态，均为32位。目前只使用了一部分。##寄存器组织
+ 通用寄存器	+ **不分组寄存器**(R0~R7)
	+ **分组寄存器**(R8~R14)
	+ **程序计数器**(R15)+ 程序状态寄存器(CPSR和SPSR)
	+ 条件码标识
		+ N(**N**egative): 如果结果是带符号二进制补码，则N=1, 若结果为正数或0，N=0；
		+ Z(**Z**ero): 若指令的结果为0，则置1，否则置0。
		+ C(**C**arry): 以下3种之一：
			+ 加法（包括CMN），若加法产生进位，则置C为1。
			+ 减法（包括CMP），若减法产生借位，则置C为0。
			+ 对于移位操作，位移出的最后1位。
		+ V(o**V**erflow) : 对于加法或减法指令，当发生带符号溢出时，V置1，表示操作数和结果是补码形式的带符号整数。	+ 控制位
		+ 中断禁止位：I=1 禁止IRQ中断；F=1 禁止FIQ中断。
		+ T位： T=0 指示ARM执行；T=1 指示Thumb执行。
		+ 模式位（M4、M3、M2、M1、M0）
		##处理器的运行模式
+ **用户模式**(usr)：ARM的正常程序执行模式。
+ **快速中断模式**(fiq)：用于高速数据传输或通道处理。
+ **外部中断模式**(irq)：用于通用的中断处理。
+ **管理模式**(svc)：操作系统使用的保护模式。
+ **数据访问中止模式**(abt)：当数据或指令预取终止时进入该模式，可用于虚拟存储及存储保护。
+ **系统模式**(sys)：运行具有特权的操作系统任务。
+ **未定义指令中止模式**(und)：当未定义的指令执行时进入该模式，可用于支持硬件协处理器的软件仿真。
**除用户模式外，均为特权模式。除用户模式，系统模式外，均为异常模式。**##处理器的工作状态
ARM微处理器有**32位ARM**和**16位Thumb**两种工作状态。在32位ARM状态下执行字对齐的ARM指令，在16位Thumb状态下执行半字对齐的Thumb指令。
+ 进入Thumb状态	+ 执行BX指令，当操作数寄存器的位[0]为1时，则微处理器从ARM状态切换到Thumb状态。此为主动切换。	+ 当处理器处于Thumb状态时发生异常（如IRQ、FIQ、Undef等），处理完异常后，在异常处理返回时，自动切换到Thumb状态。此为自动切换。+ 进入ARM状态	+ 执行BX指令，当操作数寄存器的位[0]为0时，则微处理器从Thumb状态切换到ARM状态。此为主动切换。
	+ 当处理器在Thumb状态时发生异常（如IRQ、FIQ、Undef等），则处理器从Thumb状态自动切换到ARM状态进行异常处理。
在Thumb状态中，高寄存器（R8～R15）不是标准寄存器集的一部分。汇编语言程序员对它们的访问受到限制，但可以将它们用于快速暂存。可以使用MOV、CMP和ADD指令对高寄存器操作。

	;从ARM状态切换到Thumb状态		LDR		R0,	=Lable+1		BX		R0	;从Thumb状态切换到ARM状态		LDR		R0,	=Lable		BX		R0                        注意：

+ ARM和Thumb之间状态的切换不影响处理器的模式或寄存器的内容。+ ARM处理器在开始执行代码时，只能处于ARM状态。

##处理器的寄存器组织
R13:
| 地址 |程序代码    |流水线状态||:---:|:---------:|:------:|| PC-8| LDR R0,PC |正在执行 |
| PC-4| ???       |正在译码 |
| PC  | ???       |正在取指 |

+ ARM

	| 用户| 系统|管理 |中止 |未定义 |中断 |快中断 |
|:-----:|:-----:|:------:|:------:|:------:|:------:|:-----:|
| R0    | R0    | R0     | R0     | R0     | R0     | R0    |
| R1    | R1    | R1     | R1     | R1     | R1     | R1    |
| R2    | R2    | R2     | R2     | R2     | R2     | R2    |
| R3    | R3    | R3     | R3     | R3     | R3     | R3    |
| R4    | R4    | R4     | R4     | R4     | R4     | R4    |
| R5    | R5    | R5     | R5     | R5     | R5     | R5    |
| R6    | R6    | R6     | R6     | R6     | R6     | R6    |
| R7    | R7    | R7     | R7     | R7     | R7     | R7    |
| R8    | R8    | R8     | R8     | R8     | R8     | R8_fiq|
| R9    | R9    | R9     | R9     | R9     | R9     | R9_fiq|
| R10   | R10   | R10    | R10    | R10    | R10    |R10_fiq|
| R11   | R11   | R11    | R11    | R11    | R11    |R11_fiq|
| R12   | R12   | R12    | R12    | R12    | R12    |R12_fiq|
| R13   | R13   |R13_svc |R13_abt |R13_und |R13_irq |R13_fiq|
| R14   | R14   |R14_svc |R14_abt |R14_und |R14_irq |R14_fiq|
| PC    | PC    | PC     | PC     | PC     | PC     | PC    |
|       |       |        |        |        |        |       |
|CPSR   |CPSR   |CPSR    |CPSR    |CPSR    |CPSR    |CPSR   |
|       |       |SPSR_svc|SPSR_abt|SPSR_und|SPSR_irq|SPSR_fiq|


+ Thumb

	| 用户| 系统|管理 |中止 |未定义 |中断 |快中断 |
|:-----:|:-----:|:------:|:------:|:------:|:------:|:-----:|
| R0    | R0    | R0     | R0     | R0     | R0     | R0    |
| R1    | R1    | R1     | R1     | R1     | R1     | R1    |
| R2    | R2    | R2     | R2     | R2     | R2     | R2    |
| R3    | R3    | R3     | R3     | R3     | R3     | R3    |
| R4    | R4    | R4     | R4     | R4     | R4     | R4    |
| R5    | R5    | R5     | R5     | R5     | R5     | R5    |
| R6    | R6    | R6     | R6     | R6     | R6     | R6    |
| R7    | R7    | R7     | R7     | R7     | R7     | R7    |
| R13   | R13   |R13_svc |R13_abt |R13_und |R13_irq |R13_fiq|
| R14   | R14   |R14_svc |R14_abt |R14_und |R14_irq |R14_fiq|
| PC    | PC    | PC     | PC     | PC     | PC     | PC    |
|       |       |        |        |        |        |       |
|CPSR   |CPSR   |CPSR    |CPSR    |CPSR    |CPSR    |CPSR   |
|       |       |SPSR_svc|SPSR_abt|SPSR_und|SPSR_irq|SPSR_fiq|

##异常处理的类型及含义、异常向量表、优先级
+ 复位  
当处理器的复位电平有效时，产生复位异常，ARM微处理器立刻停止执行当前指令。
+ 未定义指令  
当ARM微处理器或协处理器遇到不能处理的指令时，产生未定义指令异常。
+ 软件中断  
**由SWI指令产生，可使用该异常机制实现系统功能调用，**用于系统模式下的程序调用特权操作指令，以请求特定的管理（操作系统）函数。
+ 指令预取中止  
若处理器预取的地址不存在或改地址不允许当前指令访问，存储器会向处理器发出存储器中止信号，**但当预取指令被执行时，才会产生指令预取中止异常。**
+ 数据中止  
若处理器数据访问指令的地址不存在，或改地址不允许当前指令访问时，产生数据中止异常。
+ IRQ  
当处理器的外部中断请求引脚有效，**且CPSR中I位为0时**，产生IRQ异常。**当进入FIQ处理时会屏蔽掉IRQ**。
+ FIQ  
当处理器的外部中断请求引脚有效，**且CPSR中F位为0时**，产生FIQ异常。FIQ支持数据传送和通道处理，并且由足够的私有寄存器。

|异常类型				|地址（异常向量）|进入模式	|优先级|
|:----------------:|:------:|:------------:|:----:|
|**复位**				|0x00000000	|管理模式		|1
|**数据中止**			|0x00000010	|中止模式		|2
|**FIQ(快速中断请求)**	|0x0000001C	|FIQ模式		|3
|**IRQ(外部中断请求)**	|0x00000018	|IRQ模式		|4
|**指令预取中止**			|0x0000000C	|中止模式		|5
|**未定义指令**			|0x00000004	|未定义模式	|6
|**软件中断**			|0x00000008	|管理模式	|7

##存储器结构(地址空间、存储器格式、数据类型等)。

数据类型(type): ARM处理器支持以下**6种数据类型**：  
+ 8位有符号和无符号字节（Byte）。+ 16位有符号和无符号半字（Halfword）。必须以两字节的边界对齐（半字对齐）。+ 32位有符号和无符号字（Word）。它们必须以4字节的边界对齐（字对齐）。

数据类型(length):

+ 对于指令：ARM指令系统分为32位ARM指令集和16位的Thumb指令集，在存储时分别以32位和16位的两种不同长度存储。+ 对于数据：ARM支持对32位字数据、16位半字数据、8位字节数据操作。因此数据存储器可以存储32位、16位、8位三种不同长度数据

地址：ARM体系结构使用2^32字节的单一、线性地址空间。范围为0~(2^32)-1

存储格式：大端存储和小端存储格式：

![小端存储](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/Little-Endian.svg/280px-Little-Endian.svg.png)
![大端存储](https://upload.wikimedia.org/wikipedia/commons/thumb/5/54/Big-Endian.svg/280px-Big-Endian.svg.png)


#第三章 ARM微处理器指令系统##9种寻址方式
+ **寄存器寻址**: 操作数的值在寄存器种，指令中的地址码段给出的是寄存器的编号，寄存器的内容是操作数，指令执行时直接取出寄存器值操作。
	
		MOV		R1, R2				;R1<-R2
		SUB		R0, R1, R2			;R0<-R1-R2
+ **立即寻址**:	在立即寻址中，数据就包含在指令当中，立即寻址指令的操作码字段后面地址码部分就是操作数本身，取出指令也就取出了可以立即使用的操作数。立即数以"#"为前缀。表示16进制时以"0x"表示。
		ADD		R0, R0, #1			;R0<-R0+1		MOV		R0, #0XFF00			;R0<-0XFF00
	+ **寄存器位移寻址**: 第二个寄存器操作数在与第一个操作数结合之前，先进行位移操作。
		
		MOV		R0, R2, LSL #3		;R2的值左移3位，结果放入R0，即R0=R2*8
		ADDS	R1, R1, R2, LSL R3	;R2的值左移R3位，然后和R1相“与”操作，结果放入R1		+ LSL 逻辑左移 寄存器中字的低端空出的位补0。	+ LSR 逻辑右移 寄存器中字的高端空出的位补0。	+ ASR 算术右移 移位过程中保持符号位不变。	+ ROR 循环右移 由字的低端移出的位填入字的高端空出的位。	+ RRX 带扩展的循环右移 右移1位，高位用C标志位填充。
	+ **寄存器间接寻址**：所需要的操作数保存在寄存器指定地址的存储单元中，即寄存器位操作数的地址指针，操作数存放在存储器中。
		LDR		R0, [R1]			;R0<-[R1]
		STR		R0, [R1]			;[R1]<-RO		+ **变址寻址（变址寻址）**: 将基址寄存器的内容与指令中给出的偏移量相加，形成操作数的有效地址，变址寻址用于访问基址附近的存储单元，常用于查表，数组操作，功能部件寄存器访问等。
		LDR		R2, [R3, #4]		;R2<-[R3+4]
		STR		R1, [R0, #-2]		;[R0-2]<-R1		+ **多寄存器寻址**: 采用多寄存器寻址方式，一条指令可以完成多个寄存器值的传送，这种寻址方式用一条指令最多可以完成16个寄存器值额传送。
		LDMIA	R0, {R1, R2, R3, R4}	;R1<-[R0],R2<-[R0+4],										;R3<-[R0+8],R4<-[R0+12]+ **堆栈寻址**: 堆栈寻址时隐含的，它使用一个专门的寄存器（堆栈指针）指向一块存储区域，指针所指向的存储单元就是堆栈的栈顶。
	LD:Load, ST: Store, {F:Full, E:Empty, A:Asc, D:Desc} {A:After, B:Before, I:Increment, D:Decrement};

		LDMDA = LDMFA
		LDMIA = LDMFD (= pop)
		LDMDB = LDMEA
		LDMIB = LDMED
		STMDA = STMED
		STMIA = STMEA
		STMDB = STMFD (= push)
		STMIB = STMFA

	+ 满递增堆栈:LDMFA, STMFA
	+ 满递减堆栈:LDMFD, STMFD
	+ 空递增堆栈:LDMEA, STMEA
	+ 空递减堆栈:LDMED, STMED
	+ **块复制寻址**: 块寻址用于把一块充存储器的某一位置复制到另一位置，是一个多寄存器传送指令。见上。	+ **相对寻址**: 是变址寻址的一种变通，由程序计数器PC提供基准地址，指令中的地址码子段作为偏移量，两者相加后的地址即为操作数的有效地址。
				BL		ROUTE1				BEQ		LOOP		LOOP:	MOV		R2, #2
		ROUTE1:
#第四章 基于ARM的嵌入式程序设计## 要求通读章节中的ARM汇编语言的伪操作
+ GCC:由GNU的汇编器as、交叉编译器gcc和链接器ld等组成。

	|伪操作|语法格式|作用|
|:--|:--|:--|
|.section|.section    expr|定义域中包含的段。|
|.text| |.text 表示只读代码区。|
|.data| |.data 表示可读，可写数据区。|
|.bbs| |.bbs 表示为静态和全局变量保留的、未初始化的可读／写的数据区。|
|.arm/.code 32| |表示后面的汇编代码是32位的ARM的代码。|
|.thumb/.code 16||表示后面的汇编代码是16位的thumb代码。
|.end||标记汇编文件的结束行，即标号后的代码不作处理。|
|.include|.include  "filename"|将一个源文件包含到当前源文件中。
|.thumb_func||表示后面的代码是使用thumb指令集的函数。
|.equ|.equ symb,expr|.equ 伪操作为数字常量、基于寄存器的值和程序中的标号定义一个字符名称
|ldr|ldr reg,=expr|作用是将32位的立即数存放到寄存器中（MOV或MVN指令一般只能用于传送8～24位立即数）。
|.ltorg||在当前段的当前地址（字对齐）产生一个文字池。|

+ ADS:由ARM公司开发，使用了CodeWarrior公司的编译器；ARM公司最新的开发环境是RealView MDK。

	|伪操作|语法格式|作用|
|:--|:--|:--|
|LTORG|LTORG|声明一个数据缓冲池（也称为文字池）的开始。|
|MAP|MAP expr  {, base-register}|定义一个结构化的内存表（Storage Map）的首地址。
|FIELD|{label}  FIELD  expr|定义一个结构化内存表中的数据域，即表的长度。
|SPACE|{label}  SPACE  expr|分配一块连续内存单元（字节），并用0初始化。
|||
|DCB|{label} DCB expr {, expr}|分配一段字节内存单元，并用expr初始化。
|DCW/DCWU|{label}  DCW{U} expr {, expr}|分配一段半字内存单元，并用expr初始化。
|DCD/DCDU|{label}  DCD{U} expr {, expr}|分配一段字内存单元，并用expr初始化。
|DCQ/DCQU|{label}  DCQ{U} expr {, expr}|分配一段双字节内存单元，并用expr初始化。
|DCFS/DCFSU|{label}  DCFS{U} expr {, expr}|分配一段单精度内存单元，并用expr初始化。
|DCFD/DCFDU|{label}  DCFD{U} expr {, expr}|分配一段双精度内存单元，并用expr初始化。

	B:Byte/Int8, W:Word/Int16, D:Dword/Int32, Q:Qword/Int64, FS:Float Single, FD:Float Double, U:不一定对齐，如：DCDU不一定字对齐。

	例子：
	
		Str			DCB		"this is a test"
		DataTest	DCW		1,2,3
		DataSpace	SPACE	100				；分配连续100字节的存储单元并初始化为0。
		MAP			0X1000					；定义结构化内存表首地址的值为0X1000		Fir			FIELD	16				；定义Fir的长度为16字节，位置为0X1000		Sec			FIELD	32				；定义Sec的长度为32字节，位置为0X1010		Thr			FIELD	256				；定义Thr的长度为256字节，位置为0X1030
	## ATPCS规则

+ 寄存器的使用规则 
	+ 子程序间通过寄存器R0~R3来传递参数，可以记作A1~A4，返回前无需恢复R0～R3的内容。亦用来返回结果。	+ 子程序中，用寄存器R4~R11保存局部变量，可以记作V1~V8。如有破坏数据在返回时必须恢复。	+ R12用作子程序间临时寄存器，记作IP，	+ R13用作堆栈指针，记作SP。在子程序中，R13不能用作其他用途，进入和退出子程序时的值必须相等。	+ R14用作链接寄存器LR，一般用于保存子程序的返回地址。	+ R15是程序寄存器PC，它不能用作其他用途。
	
	|寄存器|别名|使用规则|
	|:--|:--|:--|
	|r0|a1|参数/结果／scratch寄存器1|
	|r1|a2|参数/结果／scratch寄存器2|
	|r2|a3|参数/结果／scratch寄存器3|
	|r3|a4|参数/结果／scratch寄存器4|
	|r4|v1|ARM状态局部变量寄存器1|
	|r5|v2|ARM状态局部变量寄存器2|
	|r6|v3|ARM状态局部变量寄存器3|
	|r7|v4|ARM状态局部变量寄存器4|
	|r8|v5|ARM状态局部变量寄存器5|
	|r9|v6|ARM状态局部变量寄存器6|
	|r10|v7|ARM状态局部变量寄存器7|
	|r11|v8|ARM状态局部变量寄存器8|
	|r12|ip|子程序内部调用的scratch寄存器|
	|r13|sp|数据栈指针|
	|r14|lr|连接寄存器|
	|r15|pc|程序计数器|
	
+ 数据栈的使用规则: **ATPCS规定数据栈为FD（满递减）类型**，并且对数据栈的操作是8字节对齐的(LDMIA=LDMFD=pop; STMDB=STMFD=push)。
+ 参数的传递规则
	+ 参数个数固定的子程序参数传递规则 : Parameter values are passed to a nonvariadic routine in the following way:
		1. The first integer arguments are allocated to r0-r3 in order. 但是被翻译成了(第一个整数参数，通过寄存器R0~R3来传递。其他参数通过数据栈传递。)
		2. Remaining parameters are allocated to the stack in order
	+ 参数个数可变的子程序参数传递规则 :  
	Parameter values are passed to a variadic routine in integer registers a1-a4, and on the stack if necessary.  
	The order of the words used is as if the parameter values were stored in consecutive memory words and then transferred to:
		1. a1-a4, a1 first.
		2. The stack, lowest address first. (This means that they are pushed onto the stack in reverse order.)
	+ 子程序结果返回规则  
	结果为一个32位的整数时，可以通过寄存器R0返回；结果为一个64位整数时，可以通过寄存器R0和R1返回，依次类推。
#第五章32位RISC微处理器S3C2410A## S3C2410A的内部结构## S3C2410A的技术特点+ 体系结构  
	+ 具有16位／32位RISC体系结构和强大的指令集。
	+ 增强的ARM体系结构MMU(Memory Management Unit)
+ Nand Flash Boot Loader
	+ 支持从NAND Flash存储器的启动。
	+ 采用4KB内部缓冲器用于启动引导。
	+ 支持启动之后NAND存储器仍然作为外部存储器使用。
+ cache存储器
	+ 指令和数据cache为64路组相连cache
	+ 每行长度8字，带1位有效位，2位文件系统的错误状态位。
	+ 采用写通、写回cache操作来更新主存储器。
+ 3通道UART
+ 8通道多路复用ADC
	## 存储器控制器+ 支持大小端方式
+ 每个bank有128MB，共1GB。
+ bank0~bank6都采用固定的bank起始地址。
+ bank7具有可编程的bank起始地址。
+ bank6,7大小是可编程的。
+ 总共8个bank：
	+ 6个存储器bank用于ROM,SRAM及其他存储器。
	+ 2个存储器bank用于ROM,SRAM和SDRAM。
	## 时钟电路
+ 片上MPLL和UPLL（PLL:phase locked loop,锁相环）
+ UPLL产生用于USB主机／设备操作的时钟（48MHz）。
+ MPLL产生操作MCU（Microcontroller Unit）的时钟，可达266MHz, 用于CLK、HCLK和PCLK。## 电源电路
+ 正常模式为正常运行模式
+ 慢速模式为不加锁相环(PLL)的低时钟频率模式
+ 空闲模式只停止CPU的时钟
+ 掉电模式切断所有外设和内核的电源## I/O口及相关寄存器
共有117个多功能复用输入/输出端口（即I/O口），分为端口A~端口H共**8组**。**端口A(GPA)除了作为功能口外，只能够作为输出口使用。**
与配置I/O口相关的寄存器包括:
+ **端口控制**(CONtrol)**寄存器**(GPACON~GPHCON)+ **端口数据**(DATa)**寄存器**(GPADAT~GPHDAT)+ **端口上拉**(UP)**寄存器**(GPBUP~GPHUP)(无A)+ **杂项寄存器**+ **外部中断控制寄存器**(EXTINTn)#第六章 嵌入式系统的存储器系统## 嵌入式系统存储设备层次结构![](http://img1.ph.126.net/HhSDrkpA27nylqIixmoyfQ==/6608567359748035981.png)## 存储器部件的分类
+ 按在系统中的地位分类：
	+ 主存储器（内存或主存）
	+ 辅助存储器（辅存或外存）
+ 按存储介质分类：
	+ 磁存储器
	+ 半导体存储器
	+ 光存储器
	+ 激光光盘存储器
+ 按信息存取方式分类：
	+ 随机存取存储器(RAM)
	+ 只读存储器(ROM)
	## 常见的嵌入式系统存储器（RAM、ROM、Flash Memory）
+ RAM  
    RAM可以被读和写，地址可以以任意次序被读。常见RAM的种类有SRAM（Static RAM，静态随机存储器）、DRAM（Dynamic RAM，动态随机存储器）、DDRAM（Double Data Rate SDRAM，双倍速率随机存储器）。其中，SRAM比DRAM运行速度快，SRAM比DRAM耗电多，DRAM需要周期性刷新。而DDRAM是RAM的下一代产品。
+ ROM  
    ROM在烧入数据后，无需外加电源来保存数据，断电后数据不丢失，但速度较慢，适合存储需长期保留的不变数据。在嵌入式系统中，ROM用固定数据和程序。
+ Flash Memory  
    Flash memory（闪速存储器），是一种非易失性存储器NVM（Non-Volatile Memory），用来存储程序和数据，掉电后数据不会丢失。分成NOR Flash和NAND Flash两种。  
    + NOR Flash把整个存储区分成若干个扇区（Sector），而NAND Flash把整个存储区分成若干个块（Block），可以对以块或扇区为单位的内存单元进行擦写和再编程。  
    + NOR Flash的读速度比NAND Flash稍快一些，NAND Flash的写入速度比NOR Flash快很多。
    + NAND的随机读取能力差，适合大量数据连续的读取。
#第八章 嵌入式系统总线接口## 异步通信方式异步通信时数据是一帧一帧传送的，每帧数据包含有起始位（“0”）、数据位、奇偶校验位和停止位（“1”） ，每帧数据的传送靠起始位来同步。一帧数据的各位代码间的时间间隔是固定的，而相邻两帧的数据其时间间隔是不固定的。在异步通信的数据传送中，传输线上允许空字符。+ 字符的格式
	每个字符传送时，必须前面加一起始位，后面加上1、1.5或2位停止位。例如ASCII码传送时，一帧数据的组成是：前面1个起始位，接着7位ASCII编码，再接着一位奇偶校验位，最后一位停止位，共10位。
+ 波特率
	传送数据位的速率称为波特率，用位/秒（bit/s）来表示，称之为波特。例如，数据传送的速率为120字符/秒，每帧包括10个数据位，则传送波特率为：          10×120=1200b/s=1200波特    每一位的传送时间是波特的倒数，如1/1200=0.833ms。异步通信的波特率的数值通常为：150、300、600、1200、2400、4800、9600、14400、28800等，数值成倍数变化。
+ 校验位
	在一个有8位的字节（byte）中，其中必有奇数个或偶数个的“1”状态位。对于偶校验就是要使字符加上校验位有偶数个“1”；奇校验就是要使字符加上校验位有奇数个“1”。例如数据“00010011”，共有奇数个“1”，所以当接收器要接收偶数个“1”时（即偶校验时），则校验位就置为“1”，反之，接收器要接收奇数个“1”时（即奇校验时），则校验位就置为“0”。  ## RS-232C接口信号线RXD和TXD
+ RXD(Receive Data) 接收数据 2引脚
+ TXD(Transmit Data) 发送数据 3引脚

简单连接：2↔3, 5↔5
完全连接：2↔3, 4↔6, 5↔5, 7↔8
## UART的概念、传送数据的格式、UART结构

UART以字符为单位进行数据传输。包括

+ 线路空闲位(高电平)
+ 起始位(低电平)
+ 数据位(5~8位数据位)
+ 校验位(可选)
+ 停止位(1位，1.5位或者2位)
![uart](http://blog.chinaunix.net/attachment/201103/14/24148050_1300084125LBxX.jpeg)##I2C接口基本原理与结构。
在I2C总线上，只需要两条线—***串行数据(SDA)线和串行时钟(SCL)线***，它们用于总线上器件之间的信息传递。SDA和SCL都是双向的。每个器件都有一个唯一的地址以供识别，而且各器件都可以作为一个发送器或接收器（由器件的功能决定）。I2C总线有如下操作模式：主发送模式、主接收模式、从发送模式、从接收模式。

![i2c](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/I2C.svg/300px-I2C.svg.png)+ 在传输数据的时候，SDA线必须在时钟的高电平周期保持稳定，SDA的高或低电平状态只有在SCL 线的时钟信号是低电平时才能改变 。
+ SCL 线是高电平时，SDA 线从高电平向低电平切换，这个情况表示起始条件；

+ SCL 线是高电平时，SDA 线由低电平向高电平切换，这个情况表示停止条件。
![i2c2](https://upload.wikimedia.org/wikipedia/commons/thumb/6/64/I2C_data_transfer.svg/600px-I2C_data_transfer.svg.png)
# 冯诺依曼结构与哈佛结构的定义及特征## 冯诺依曼结构:+ **单一存储、统一编址、分时复用**+ **数据与指令都存储在同一存储区中，取指令与取数据利用同一数据总线。**+ 结构简单，但速度较慢。**取指不能同时取数据**。
+ ARM7	## 哈佛结构:+ **分开存储、独立编址、两倍带宽、执行效率更高**+ **程序存储器和数据存储器是两个相互独立的存储器，每个存储器独立编址、独立访问**
+ **系统中具有程序的数据总线与地址总线，数据的数据总线与地址总线。**+ **将程序和数据存储在不同的存储空间中**
+ 适合于数字信号处理；+ ARM9: 取指和取数在同一周期进行，提高速度。(五级流水线)		
# 异常的响应过程及返回过程
## 异常的响应过程
1. 将CPSR复制到将要执行的异常中断对应的SPSR中；
+ 设置CPSR
	+ 如为中断，将对应开关置1；
	+ 如在thumb，将T开关置0；
	+ 将MOD置为与异常类型相对应的运行状态的值
+ 在异常对应运行状态对应的LR中保存下一条指令的地址；
+ 异常向量存入PC。

## 异常的返回过程
1. 所有被修改过的用户寄存器必须从处理程序的保护堆栈中恢复回来（即出栈）(FIQ的R8_fiq～R12_fiq除外)
+ 将SPSR_mode寄存器内容复制到CPSR中(恢复CPSR的动作会将T、F和I位自动恢复为异常发生前的值。)
+ 将LR中的值减去偏移量（偏移量根据异常类型的不同而有所不同）后存入PC，即转到产生异常前用户指令流中相应指令处；

# ARM指令集
##指令的基本格式
	<opcode>{<cond>}{S}<Rd>,<Rn>{,<opcode2>}
+ < opcode > 是指令注记符
+ < cond > 是指令执行条件
+ S 为是否影响CPSR寄存器的值
+ Rd 为目标寄存器
+ Rn 为第一个操作数的寄存器
+ opcode2 为第二个操作数
##条件码
|条件码|后缀|标志|含义|
|:--:|:--|:--|:--||0000|EQ|Z=1|相等|0001|NE|Z=0|不相等|0010|CS/HS|C=1|无符号数大于或等于|0011|CC/LO|C=0|无符号数小于|0100|MI|N=1|负数|0101|PL|N=0|正数或零|0110|VS|V=1|溢出|0111|VC|V=0|没有溢出|1000|HI|C=1,Z=0|无符号数大于|1001|LS|C=0,Z=1|无符号数小于或等于|1010|GE|N=V|有符号数大于或等于|1011|LT|N!=V|有符号数小于|1100|GT|Z=0,N=V|有符号数大于|1101|LE|Z=1,N!=V|有符号数小于或等于|1110|AL|任何|无条件执行（指令默认条件）##指令

###load/store指令
	LDR	R0，[R1]		；R0←[R1]    STR	R0，[R1]		；[R1]←R0

+ LDR
	+ LDR 加载字 LDRNEH R0, [LR,#-2]
	+ LDRH 加载半字
	+ LDRB 加载字节
+ STR
	+ STR 加载字 LDRNEH R0, [LR,#-2]
	+ STRH 加载半字
	+ STRB 加载字节
+ LDM, STM 见寻址，高寄存器先入栈

###加减法指令
|助记符|说明|操作|条件码位置|
|:--|:--|:--|:--|
|ADD   Rd, Rn, operand2|加法运算指令|Rd←Rn+operand2|ADD{cond}{S}|
|SUB   Rd, Rn, operand2|减法运算指令|Rd←Rn-operand2|SUB{cond}{S}|
|RSB   Rd, Rn, operand2|逆向减法指令|Rd←operand2-Rn|RSB{cond}{S}|
|ADC   Rd, Rn, operand2|带进位加法|Rd←Rn+operand2+Carry|ADC{cond}{S}|
|SBC   Rd, Rn, operand2|带进位减法指令|Rd←Rn-operand2-(NOT)Carry|SBC{cond}{S}|
|RSC   Rd, Rn, operand2|带进位逆向减法指令|Rd←operand2-Rn-(NOT)Carry|RSC{cond}{S}|

###MOV
MOV{cond}{S}    Rd,operand2

	MOV		R1,#0x10		;R1←0x10     MOV		R0,R1			;R0←R1     MOVS	R3,R1,LSL #2	;R3←R1<<2，并影响标志位 

###BIC
BIC{cond}{S}    Rd,Rn, operand2   
		
	BIC     R1,R1,#0x0F	;将R1的低4位清零，其它位不变 
	BIC     R1,R2,R3	;将R3的反码和R2相逻辑“与”，						;结果保存到R1中
			
###B
1. B：转移指令 2. BL：带链接的转移指令 3. BX：带状态切换的转移指令 4. BLX：带链接和状态切换的转移指令 
###MRS, MSR1. MRS: 状态寄存器到通用寄存器的传送指令2. MSR: 通用寄存器到状态寄存器的传送指令
# 弄懂实验一和实验二内容
##code1

	.global _start
	.text
		.equ num,20					/* Set number of words to be copied */
	_start:
		ldr		r0, =src			/* r0 = pointer to source block */
		ldr		r1, =dst			/* r1 = pointer to destination block */
		mov		r2, #num			/* r2 = number of words to copy */
		mov		sp, #0x400			/* set up stack pointer (r13) */
	blockcopy:
		movs	r3,r2, LSR #3		/* number of eight word multiples */
		beq		copywords			/* less than eight words to move ? */
		stmfd	sp!, {r4-r11}		/* save some working registers */
	octcopy:
		ldmia	r0!, {r4-r11}		/* load 8 words from the source */
		stmia	r1!, {r4-r11}		/* and put them at the destination */
		subs	r3, r3, #1			/* decrement the counter */
		bne		octcopy				/* ... copy more */
		ldmfd	sp!, {r4-r11}		/* don't need these now - restore originals */
	copywords:
		ands	r2, r2, #7			/* number of odd words to copy */
		beq		stop				/* No words left to copy ? */
	wordcopy:
		ldr		r3, [r0], #4		/* a word from the source */
		str		r3, [r1], #4		/* store a word to the destination */
		subs	r2, r2, #1			/* decrement the counter */
		bne		wordcopy			/* ... copy more */
	stop:
		b		stop
		
	.ltorg
	src:
		.long	1,2,3,4,5,6,7,8,1,2,3,4,5,6,7,8,1,2,3,4
	dst:
		.long	0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
	.end	
	
##code2

	.equ x, 45
	.equ y, 64
	.equ stack_top, 0x1000
	.global _start
	.text
	_start:
		mov		sp, #stack_top
		mov		r0, #x				/* put x value into R0 */
		str		r0, [sp]			/* save the value of R0 into stacks */
		mov		r0, #y				/* put y value into R0 */
		ldr		r1, [sp]			/* read the data from stack,and put it into R1 */
		add		r0, r0, r1
		str		r0, [sp]
	stop:
		b		stop
	.end
	
##code3
	.global _start
	.text
	.equ num,2
	_start:
		mov 	r0, #0		/* set up the three parameters */
		mov 	r1, #3
		mov 	r2, #2
		bl 		arithfunc	/* call the function */
	stop:	
		b 		stop
	arithfunc:				# According R0 valude to execute the code
		cmp		r0, #num
		bhs		DoAdd		/* If code is >=2 then do operation 0. */
		
		adr		r3, JumpTable		/* Load address of jump table */
		ldr		pc, [r3,r0,lsl#2]	/* Jump to the appropriate routine */
	JumpTable:
		.long	DoAdd
		.long	DoSub
		
	DoAdd:
		add		r0, r1, r2
		mov 	pc, lr
	DoSub:
		sub		r0, r1, r2
		mov		pc, lr
	.end
# ARM汇编语言与C语言的混合编程。# S3C2410A中断控制器的内部结构和特殊寄存器功能。##S3C2410A中断控制器的特殊寄存器功能
|寄存器|地址|R/W|描述|复位值|
|:--:|:--|:--:|:--|:--:|
|SRCPND|0X4A000000|R/W|中断源挂起寄存器，为0时，无中断请求；当有中断产生，相应位置1。所有来自中断源的中断请求首先被登记到中断源挂起寄存器中。|0x00000000|
|INTMOD|0X4A000004 |R/W|中断模式寄存器：0=IRQ模式，1=FIQ模式。多个IRQ中断的仲裁过程在优先级寄存器进行。|0x00000000|
|INTMSK|0X4A000008 |R/W|中断屏蔽寄存器：0=允许中断，1=屏蔽中断。中断屏蔽寄存器的主要功能是屏蔽相应中断的请求，即使中断挂起寄存器的相应位已经置1，也就是说已经有相应的中断请求发生了；但是如果此时中断屏蔽寄存器的相应位置1，则中断控制器将屏蔽该中断请求CPU不会响应该中断。|0xFFFFFFFF||PRIORITY|0x4A00000C |R/W|IRQ中断优先级控制寄存器|0x7F|
|INTPND|0X4A000010 |R/W|中断状态指示寄存器：0=该中断没有请求，1=该中断源发出中断请求|0x00000000|
|INTOFFSET|0X4A000014 |R|中断偏移寄存器，指示IRQ中断源 |0x00000000|
|SUBSRCPND|0X4A000018 |R/W|子中断源状态寄存器，指示中断请求的状态，只使用[0～10]位，其它未用。0=该中断没有请求，1=该中断源发出中断请求|0x00000000|
|INTSUBMSK|0X4A00001C |R/W|定义哪几个中断源屏蔽，只用[0～10]。0=中断服务允许，1=中断服务屏蔽|0x7FF|
# S3C2410A与外部中断有关的控制寄存器及其特点##S3C2410A与外部中断有关的控制寄存器
|寄存器|地址|读/写|描述|复位值|
|:--|:--|:--|:--|:--||EXTINT0|0x56000088|R/W|外部中断控制寄存器0 ，使用位[30:0]，分别对EINT7～EINT0触发信号进行配置。<br>000：低电平触发；001：高电平触发；<br>01x：下降沿下降；10x：上升沿触发；<br>11x：双边沿触发<br>位3、7、11、15、19、23和27没有使用。|0x0|
|EXTINT1|0x5600008C|R/W|外部中断控制寄存器1，使用位[30:0]，分别对EINT15～EINT8触发信号进行配置。<br>000：低电平触发；001：高电平触发；<br>01x：下降沿下降；10x：上升沿触发；<br>11x：双边沿触发<br>位3、7、11、15、19、23和27没有使用。|0x0|
|EXTINT2|0x56000090|R/W|外部中断控制寄存器2，使用位[30:0]，分别对EINT23～EINT16触发信号进行配置。<br>000：低电平触发；001：高电平触发；<br>01x：下降沿下降；10x：上升沿触发；<br>11x：双边沿触发<br>其中位31、27、23、19、15、11、7和3为EINT23～EINT16滤波器使能控制。<br>1：使能；0：不使能|0x0|
# NAND Flash控制器特性、自动启动模式的时序。
##NAND Flash控制器特性+ NAND Flash模式：支持读/擦除/编程NAND Flash存储器。+ 自动启动模式：复位后，启动代码被传送到Steppingstone中。传送完毕后，启动代码在Steppingstone中执行。+ 具有硬件ECC产生模块（硬件生成校验码和通过软件校验）。+ 在NAND Flash启动后，Steppingstone 4KB内部SRAM缓冲器可以作为其他用途使用。+ NAND Flash控制器不能通过DMA访问，可以使用LDM/STM指令来代替DMA操作。
##NAND 自动启动模式的时序+ 完成复位；+ 当自动启动模式使能时，首先将NAND Flash存储器的前4 KB内容自动复制到Steppingstone 4KB内部缓冲器中；+ Steppingstone映射到nGCSO；+ CPU开始执行在Steppingstone 4KB内部缓冲器中的启动代码。