---
title: 卫生统计及SAS应用（一）数据集的建立
date: 2020-12-05T02:19:04.000Z
tags: []
published: true
hideInList: false
feature: null
isTop: false
---

# Base: import, format and export

## 1. Lesson 1 数据集的建立

### 1.1. 简介（ 程序的基本组成）

#### 1.1.1. 步（STEP）

* 数据步

```
data 数据集名; /*新建数据集，导入储存数据*/
```

* 过程步

```
proc 操作 <options>; /*处理数据*/
run;/*结束语*/
```

#### 1.1.2. 语句（STATEMENT）

相邻两个分号之间是语句。

#### 1.1.3. 选项（OPTION）

语句中有选项。

* 必选选项
* 可选选项

### 1.2. 获取数据集的方式

#### 1.2.1. 使用SAS的数据集

#### 1.2.2.直接输入数据

```sas
data ma.fsh;                     /*数据集起名为ma.fsh，ma是逻辑库名，fsh是ma中的某一数据集名*/
input age weight height;         /*数据集中输入3个变量，命名为age、weight、height*/
cards;                           /*标志着后面就是数据输入；或者用 dataline */
26 62 171
28 58 166
;
```

注意事项

* 变量之间用空格作为分隔符。
* 任何语句的结束需要加一个分号“;”，必须是英文状态下的分号。
* 数据输入后，分号要另起一行，不能紧接在数据后输入。
* 输入的字母不区分大小写
* 输入格式不要求必须左对齐或右对齐
* 初学者尽量不要把多个语句放在一行。如：
  * Input id age; cards; 1 23; 初学者最好不要用这种输入方式
* SAS数据集名或变量名太长时，可加下划线，如:
  * Data drinking\_in\_beijing; Input test\_score;

#### 1.2.3. 用Raw数据中创建数据集

raw data 即.dat文件。

#### 1.2.4. 将其他软件数据文件转为SAS数据集

#### 1.2.5. 读取其他软件数据文件

* PROC IMPORT

```sas
/* general form */
proc import datafile="filename" out=data_set 
	dbms=identifier replace;
datarows=n;
delimiter="character";
getnames=no;
guessingrows=n;
/* example */
proc import datafile="/home/" out=data_set 
	dbms=identifier replace;
datarows=n;
delimiter="character";
getnames=no;
guessingrows=n;
```

* DATA

#### 1.2.6. 引用外部数据--菜单栏操作

* 支持的数据格式包括：SAS(.sas7bat), Excel(.xlsx), Access(), Epidata, SPSS(), 分隔符文本(.txt, .csv),....

文件--导入数据

####

### 1.3. 数据变量类型

SAS中的变量只有两种类型：字符型（character）和数值型（numeric）。其中数值型包括非日期与日期。

日期是被看作是数值型，所有日期型变量被作为是输入日期与1960年1月1日之差。如1980-1-1， SAS会认为这个值是7305。

不同类型的变量在input语句输入时需要指定相应的格式。

* 字符型character 详见 \[\[#1.4.1. 字符型输入和输出格式]]
* 数值型numeric（注意输入与输出格式的区别）
  1. 数值（不含日期）详见\[\[#1.4.2. 数值型的输入和输出格式]]
  2. 日期 详见 \[\[#1.4.3. 日期型的输入和输出格式]]

### 1.4. 变量输入和输出格式

1. 输入格式是让SAS以该格式读取，输出是让SAS以该格式显示给用户，但本身数据还是以输入格式读取后的类型保存。
2. 数值型（不含日期）、日期型、字符型，均有输入和输出格式。
3. 以日期型输入的变量，实际上是以数字来保存，0对应的日期是1960-01-01。

#### 输入格式概括

两种方法：

* 在input语句中定格式：`input variable_name variable_format`
* input + informat ：`informat variable_name variable_format` 如果不想改变输入值，则不要在输入格式中定义宽度。

```sas
data test;
/*1st way 使用input*/
input date yymmdd10.;

/*2nd way 使用input+informat*/
input date;
informat date yymmdd10.;
```

#### 输出格式概括

形式为`format variable_name variable_format`。同输入格式，但内容有一些差别。 位置在data步，`format`语句的使用必须在input语句与cards语句之间。

```sas
data test;
input x;
format yymmdd10.;
cards;
20100101
20111103
;
```

#### 1.4.1. 字符型输入和输出格式

字符型的输入和输出格式的表示方式一致。 字符型变量一定要在变量后面加一个`$`符号，变量和`$`之间空格可有可无。 完整的格式应该是 `$ < w. >`，输入和输出格式一样。 必须在变量名后加"`$`"；`w`表示字节数（1个中文占2个字节）。

* 输入格式：默认只读取前8位，==超出8位==必加上“`w.`” 。
* 输出格式：不限制输出位数。

> 示例程序

```sas
data fh;
input gender $;   /*表示gender变量是个字符型*/ 
cards;
female
male
;
```

> 示例程序

```sas
data city;
input city$ code$;
format city $4. code $2.; /*city的输出格式为字符型且字节数为4；code的输出格式为字符型且字节数为2*/
cards;
北京市 110000
天津市 120000
上海市 310000
;
proc print;
run;
```

过程：input语句中，输入格式无限制。format语句是输出格式，限制city占位4个（汉字2个），code占位2个。 结果：

| Obs                    | city | code |
| ---------------------- | ---- | ---- |
| 1                      | 北京   | 11   |
| 2                      | 天津   | 12   |
| 3                      | 上海   | 31   |
|                        |      |      |
| ### 1.4.2. 数值型的输入和输出格式 |      |      |

* 如果想让SAS有选择地读取你的数值，可以在变量后加上`w.d`的格式。其中，w表示数值的位数（包含数字和符号），如2.3的位数是3；d表示数值的小数点位数。
* 数值型的输入和输出格式不一致。
* 输入格式
  * `w.d`， 总位数w，其中小数点后位数d（小数点算1bit）
* 输出格式
  * `w.d`
  * `commaw.d` 整数部分加逗号（`,`算1个bit）
  * `percentw.d` 百分数形式（`%`算3个bit）
  * `dollarw.d` 货币（美元）

**w.d 带小数的数字**

w.d的理解：

* w是指数值从左到右读取的位数，包括==小数点、逗号==！
* d是指小数点后的位数！
* _整数若不带有小数点，则在读取时被认为==小数部分==_。

**commaw.d 标准形式表示的数字（带逗号分隔的数字）**

commaw.d将整数部分按⬅️方向隔3位加逗号（comma），当数值位数较多时，这是比较标准的表示方式。

> 示例程序

```sas
data wt;
input num cost;
format num 5.2 cost comma12.1; /*num共5位其中2位小数；cost共12位其中1位小数，且用逗号每3位分隔*/
cards;
50 10205600
45 9580000
;
proc print;
run;
```

> 示例程序

```sas
data fh;
input x 4.2;      /*变量后的4.2表示变量x的宽度共4位，其中小数点2位*/
cards;
12
2.1
15.6
23.46
;
proc print;
run;
```

> 示例程序 当输出格式设定是**comma7.1**，输入不同位数的数值后的输出结果。

```sas
data work.a;
input x; /*x的输入方式为数值型*/
format x comma7.1; /*x的输出方式为 comma7.1*/
cards;
11
111
1111
11111
111111
1111111
11111111
111111111
;
proc print;
run;
```

过程：

| 输入                                                                                                                                              | 输出      | 理解                              |
| ----------------------------------------------------------------------------------------------------------------------------------------------- | ------- | ------------------------------- |
| 111                                                                                                                                             | 111.0   | 加1位小数后，仍满足不超过7位字符               |
| 1111                                                                                                                                            | 1,111.0 | 加1位小数，加分隔符后，仍满足不超过7位字符          |
| 11111                                                                                                                                           | 11111.0 | 加1位小数，加分隔符后，超过7位字符，于是不加分隔符      |
| 111111                                                                                                                                          | 111111  | 加1位小数后，超过7位字符，于是不加小数            |
| 1111111                                                                                                                                         | 1111111 | 加1位小数后，超过7位字符，于是不加小数            |
| 11111111                                                                                                                                        | 1.111E7 | 本身输入就超过7位字符，于是改为科学记数法，且总体保留7位字符 |
| 111111111                                                                                                                                       | 1.111E8 | 本身输入就超过7位字符，于是改为科学记数法，且总体保留7位字符 |
|                                                                                                                                                 |         |                                 |
| #### dollarw.d 带货币符号的价格                                                                                                                         |         |                                 |
| 参考                                                                                                                                              |         |                                 |
| https://support.sas.com/documentation/cdl\_alternate/zt/nlsref/64811/HTML/default/p0eq0k53tsx9cwn1hnz4s942vxzm.htm#n052d5t6ckdsiwn1ubqiq4mpvgk0 |         |                                 |

1. dollarw.d 是SAS自带的货币格式
2. 指定地区：本土化与唯一
3. 通过proc format来自定义货币格式
4. dollarw.d 是SAS自带的货币格式

> 示例代码

```sas
data a;
	input x 10.; 
	*input x 10.2; /* 错误示范 */
	format x dollar10.5;
	cards;
	0.1
	1
	11
	111
	1111
	11111
	111111
	1111111
	11111111
	111111111
	1111111111
	11111111111
	;
proc print;
run;
```

| 输入                                                                | 输出                                              | input语句对数字的影响                                                           |   |
| ----------------------------------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------- | - |
| ![](assets/tmp1668631064982\_SAS-cdc-biostat-sas-1\_image\_1.png) | ![](assets/Pasted%20image%2020221117165836.png) | input语句决定了，输入时没有小数点的情况下末尾数字的位置由w.d中的d所决定。`$`算在w位中。当数字部分占位不少于w时，没有`$`符号。 |   |
|                                                                   | ![](assets/SAS-cdc-biostat-sas-1\_image\_1.png) | 当input语句的输入格式为10.2时的错误示范。                                               |   |

1. 指定地区：本土化与唯一

(1)本土化-欧元 ，结合options locale=

```sas
options locale=English_UnitedKingdom;
data _null_;
x=12345;
put x euro10.2;
run;
```

output: €12.345,00

(2)本土化- NLMNY，结合options locale= NLMNYw.d 和 NLMNYIw.d 輸出格式和輸入格式的引進是要以兩種形式來表示本土化的貨幣。

```sas
options locale=english_UnitedStates; data _null_; x=12345; put x nlmny15.2; run;
```

output: $12,345.00

(3)本土化- NLMNYI，结合options locale=

```sas
options locale=english_UnitedStates;
data _null_;
x=12345; put x nlmnyi15.2;
run;
```

output: USD12,345.00

(4)唯一的国际货币表示法- NLMNIISOw.d

```sas
data _null_; x=12345; put x nlmniusd15.2; run;
```

output: USD12,345.00

| 序号 | SAS自带货币格式                                     | 具体内容                                                              |
| -- | --------------------------------------------- | ----------------------------------------------------------------- |
| 1  | 本土化-欧元 ，结合options locale=                     | `locale=English_UnitedKingdom` , `eurow.d`, output: €12.345,00    |
| 2  | 本土化- NLMNY，结合options locale=                  | ![](assets/SAS-cdc-biostat-sas-1\_image\_2.png)                   |
| 3  | 本土化- NLMNYI，结合options locale=                 | ![](assets/SAS-cdc-biostat-sas-1\_image\_3.png)                   |
| 4  | 唯一的国际货币表示法- NLMNIISOw.d， 不需要结合options locale= | ![](assets/tmp1668674968005\_SAS-cdc-biostat-sas-1\_image\_3.png) |

1. 通过proc format来自定义货币格式

```sas
proc format; picture xxx; run; 
data a; input xxx; format xxx; 
proc print; run;`
```

> 示例

```sas
proc format;

picture aud low-<0='0,000,000,009.00'
(prefix='-AU$' mult=100)
0–high='0,000,00,009.00 '
(prefix='AU$' mult=100);

picture sfr low-<0='0,000,000,009.00'
(prefix='-SFr.' mult=100)
0–high='0,000,00,009.00 '
(prefix='-SFr.' mult=100);

picture bpd low-<0='0,000,000,009.00'
(prefix='-BPd.' mult=100)
0–high='0,000,00,009.00 '
(prefix='BPd.' mult=100);

run;
data currency;
input aud sfr bpd 12.2;
datalines;
12345 12345 12345 
0 0 0
-12345 -12345 -12345
;

proc print data=currency noobs;
var aud sfr bpd;
format aud aud. sfr sfr. bpd bpd.;
title 'Unique Currency Formats';
run;
```

输出 ![](assets/Pasted%20image%2020221117170754.png)

#### 1.4.3. 日期型的输入和输出格式

> 日期型有2种表示方法：数字、日期格式。 日期型变量输入格式与输出格式大致相同。

* 输入格式
* 输出格式允许==在宽度值前加一个字母==，表示日期的分隔符号。但不能用于date7.或date9.等格式。

**日期格式（输入、输出）**

> 只有数值型变量能输出为日期型，字符型变量则会报错，以原样输出。

| 日期格式 | 输入格式      | 举例         | 备注 |
| ---- | --------- | ---------- | -- |
| 日月年  | DDMMYY6.  | 280713     |    |
|      | DDMMYY8.  | 28/07/13   |    |
|      | DATE7.    | 28JUL13    | \* |
|      | DDMMYY8.  | 28072013   |    |
|      | DDMMYY10. | 28/07/2013 |    |
|      | DATE9.    | 28JUL2013  | \* |
| 月日年  | MMDDYY6.  | 072813     |    |
|      | MMDDYY8.  | 07/28/13   |    |
|      | MMDDYY8.  | 07282013   |    |
|      | MMDDYY10. | 07/28/2013 |    |
| 年月日  | YYMMDD6.  | 130728     |    |
|      | YYMMDD8.  | 13/07/28   |    |
|      | YYMMDD8.  | 20130728   |    |
|      | YYMMDD10. | 2013/07/28 |    |
| 月年   | MONYY5.   | JUL13      | \* |
|      | MONYY7.   | JUL2013    | \* |

**分隔符号（仅输出）**

> 以YYMMDD8.为例。 也可用于其他日期格式（除备注带星（date7. / date9. / monyy5. / monyy7. ）之外）

| 字母 | 含义         | ==日期输出格式==    | 结果显示     |
| -- | ---------- | ------------- | -------- |
| s  | slash(/)斜杠 | YYMMDD==s==8. | 13/07/28 |
| d  | dash(-)短横线 | YYMMDD==d==8. | 13-07-28 |
| p  | point(.)点  | YYMMDD==p==8. | 13.07.28 |
| c  | colon(:)冒号 | YYMMDD==c==8. | 13:07:28 |
| b  | blank( )空格 | YYMMDD==b==8. | 13 07 28 |
| n  | nothing()无 | YYMMDD==n==8. | 20130728 |

> 示例程序

```sas
data fh;
input x ddmmyy10.;  /*输入格式设置是日期型*/
/* input x $; /*假若输入格式设置是字符型 */ 
/*input x; /*假若输入格式设置是数值型非日期，观察3种输入格式下分别产生的结果 */
format x ddmmyys10.;   /*输出格式，观察是否有该语句分别产生的结果*/
cards;
22012022
23042021
;
proc print;
run;
```

format语句将其数值转为日期，若按照0所对应的日期是1960年1月1日，则该数值所对应的日期则超出可显示的范围。

| 输入格式   | 有format语句                                                            | 无format语句                                                                            |
| ------ | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 字符型    | !\[\[assets/tmp1668672857365\_SAS-cdc-biostat-sas-1\_image\_3.png]]  | 同左                                                                                   |
| 数值型非日期 | !\[\[assets/tmp1668674969008\_SAS-cdc-biostat-sas-1\_image\_4.png]]] | 数值过大，其对应的日期超出显示范围!\[\[assets/tmp1668630840044\_SAS-cdc-biostat-sas-1\_image\_3.png]] |
| 日期型    | !\[\[assets/tmp1668629937071\_SAS-cdc-biostat-sas-1\_image\_4.png]]  | !\[\[assets/tmp1668629937480\_SAS-cdc-biostat-sas-1\_image\_5.png]]                  |

#### 1.4.4. 自定义输入和输出格式：proc format

* 自定义值的内容的输入与输出格式 **invalue 与 value**
  * invalue 输入看“**原值**”
  * value 输出看“**新值**”

```sas
proc format;
invalue <$> variable_name range/value = self-defined value.../*自定义输入格式*/
value <$> variable_name range/value = self-defined value.../*自定义输出格式*/
picture module_name < range >;
```

| in/out | original   | print-string字符型                                                                           | print-numeric数值型                                                       |
| ------ | ---------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| in     | string字符型  | <p><code>invalue $grade "a"-"g"="fair" "o","u"="other"</code><br>a-g定义为fair，o/u为other</p> | <p><code>invalue grade "a"-"g"=1 other=2</code><br>a-g定义为1，其他定义为2</p>  |
| in     | numeric数值型 | <p><code>invalue $gender 1="male" 2="female"</code><br>1定义为male，2为female</p>              | <p><code>invalue lxfmt 1-4=_same_ 99=.</code><br>1-4则保持不变，99则改为缺失值</p> |
| out    | string字符型  | `value $grade "a"-"g"="fair"`                                                             | `value $grade "a"-"g"="fair" "o","u"="other"`                          |
| out    | numeric数值型 |                                                                                           |                                                                        |

* 自定义值的形式的格式 **picture**

```sas
picture 格式名 原值=新格式(prefix="￥");/*prefix表示加前缀*/

proc format;
/*对所有数值，定义格式要求：前缀为￥，每满三位数加逗号“,”*/
picture gs1 low-high="0,000,000"(prefix="￥");
/**/
picture gs2 low-high="09.99%";
run;


data profit;
input profit prop;
format profit gs1. prop gs2.;
cards;
298000 16.72
458992 21.30
;
proc print;
run;
```

结果为：

| Obs | profit   | prop   |
| --- | -------- | ------ |
| 1   | ￥298,000 | 16.72% |
| 2   | ￥458,992 | 21.30% |

说明一下，"09.99%"的理解是：

1. 先对齐小数点；
2. 再根据小数点前多少位数就保留多少位数
3. 整数部分从个位开始，小数点前多少位就保留多少位数
4. 倘若不只有一个小数点，那么先对齐最末尾的小数点；
5. 末尾加上符号（**不是把原值改为等值的百分数形式！！无论是什么符号都可以！！**）

### 1.5. 特殊变量的输入

#### 1.5.1. 给定宽度，是否取空格为字符

**（1）不取空格**

> 示例代码

```sas
data test;
input id$8. gender$1. city$7.;
/* input id$9. gender$2. city$7.; */
/* input id$8.+1 gender$1.+1 city$7.; */
/* input id:$8. gender:$1. city:$7.; */
cards;
12345678 m beijing
;
proc print;
run;
```

> 结果

| 第1个input                                          | 第2、3、4行input                                                                        |
| ------------------------------------------------- | ----------------------------------------------------------------------------------- |
| !\[\[assets/SAS-cdc-biostat-sas-1\_image\_6.png]] | !\[\[assets/SAS-cdc-biostat-sas-1\_image\_7.png]]                                   |
| 原因是指定款读后，空格也称为字符的一部分                              | <p>- 改变输入格式的==宽度（把空格也算入）==<br>- 改变变量的读取位置（==+1==表示跳过1个字符后再读取）<br>- 用==冒号==做规则读取</p> |

* 冒号的用法
  * 利用冒号（:）来正确读取（冒号表示识别前后的空格是分隔符号，不作为字符内容；否则空格也是字符的一部分）。
  * 冒号（:）的作用是告诉SAS，如果要读取下一个数据，需要满足下面任一条件：
    * 遇到空格；
    * 指定的变量宽度已满；
    * 数据行结束。

**（2）保留空格**

*   用AND（`&`）的用法

    * input语句中变量名后加 `&`。cards语句后的不同变量之间需要至少2个空格。
    * 强制保留变量中所有单词间的单个空格。
    * 连续多个空格是区分不同变量。

    ```sas
    data fh;
    input name&:$50. city&:$50.; /*变量名后加 $ */
    cards;
    Peter Parker  山东省 蓬莱市  /*两个变量之间至少2个空格*/
    Ross Geller  山东省 青岛市 市南区  
    ;
    proc print;
    run;
    ```

#### 1.5.2. 数据集中有==缺失值==

缺失值用 “**.** ”表示。

```sas
data fh;
input id day: yymmdd8. city:$20.; 
format day yymmddp10.;  
cards;
37 980515 .
. 120607 shanghai
39 . nanjing
;
proc print;
run;
```

结果为

| obs | id | day        | city     |
| --- | -- | ---------- | -------- |
| 1   | 37 | 1998.05.15 |          |
| 2   | .  | 2012.06.07 | shanghai |
| 3   | 39 | .          | nanjing  |

#### 1.5.3. 有规律的变量

用do循环输入数据

```sas
data test;
do count=3 to 9 by 2；
<语句>
end；
run；
```

其中的语句若使用input，则必须加上output。

例如1-12月是连续加1的规律。

未使用do循环时：

```sas
data test;
input count time;
cards;
1 23
2 29
3 49
4 64
5 87
6 96
;
proc print;
run;
```

由于count具有加1的规律，使用do循环后：

```sas
data test;
do count=1 to 6; /*使用do循环输入count的值*/
input time;
output; /*必须*/
end; /*必须*/
cards; /*省略count的输入*/
23
29
49
64
87
96
;
proc print;
run;
```

#### 1.5.4. 是否换行输入

用@符号

* @@与@符号的区别：
  * @@强制保持当前记录，不管data步中有没有其他语句
  * @如果data步中没有其他语句，就hold不住当前记录，仍然转到下一记录，如果有第二个input语句，就可以hold住当前记录，等待第二个input顺序读取
*   \==@@== 强行保持当前行。所输入数据均作为**某一变量**的值

    * CASE 1——录入单一变量

    ```sas
    data test;
    input weigh@@;
    cards;
    1 2 3 
    4 5 6
    ;
    proc print;
    run;
    ```

    ​ 结果为：

    | Obs | weigh |
    | --- | ----- |
    | 1   | 1     |
    | 2   | 2     |
    | 3   | 3     |
    | 4   | 4     |
    | 5   | 5     |
    | 6   | 6     |

    * CASE 2——输入列联表

    |      | 吸烟（1） | 不吸烟（2） |
    | ---- | ----- | ------ |
    | 男（1） | 100   | 150    |
    | 女（2） | 10    | 140    |

    ```sas
    /*常规输入*/
    data a;
    input gender smoking;
    cards; /*每一行表示一个个案的资料*/
    1 2
    2 1
    1 1
    1 1
    ...
    ;
    proc print;
    run;
    /*用@@输入*/
    data a;
    do gender= 1 to 2;
    do smoking= 1 to 2; /*先进行小循环，后进行大循环*/
    input x@@; /*x为频数*/
    output;
    end;
    end;
    cards;
    100 150
    10 140
    ;
    run;
    ```

    ​ 结果：

    | Obs | gender | smoking |
    | --- | ------ | ------- |
    | 1   | 1      | 2       |
    | 2   | 2      | 1       |
    | 3   | 1      | 1       |
    | 4   | 1      | 1       |
*   **@** 保持当前行

    ```
    data test;
    /*<几种不同的input方式>;*/
    cards;
    1 2 3 4
    5 6 7 8
    ;
    proc print;
    run;

    /*1*/
    input x@@;
    /*2*/
    input x;
    input y;
    /*3*/
    input x@;
    input y;
    /*4*/
    input x y;
    ```

    结果分别为：

    <1>通过@@强制将所有数据录入为值

    | Obs | x |
    | --- | - |
    | 1   | 1 |
    | 2   | 2 |
    | 3   | 3 |
    | 4   | 4 |
    | 5   | 5 |
    | 6   | 6 |
    | 7   | 7 |
    | 8   | 8 |

    <2>x与y分别在两行，各取一个值

    ```sas
    /*2*/
    input x;
    input y;
    ```

    | Obs | x | y |
    | --- | - | - |
    | 1   | 1 | 5 |

    <3>通过@保留原排列并认为第一、二列分别是x、y变量

    ```sas
    /*3*/
    input x@;
    input y;
    ```

    | Obs | x | y |
    | --- | - | - |
    | 1   | 1 | 2 |
    | 2   | 5 | 6 |

    <4>第一、二列分别是x、y变量

    ```sas
    /*4*/
    input x y;
    ```

    | Obs | x | y |
    | --- | - | - |
    | 1   | 1 | 2 |
    | 2   | 5 | 6 |

**1.5.4.1. 输入重复测量数据**

````
```sas
/*常规输入*/
data test1;
input id time score;
cards;
1 1 86
1 2 89
1 3 90
2 1 89
2 2 87
2 3 91
...
;
proc print;
run;
/*利用@符号输入*/
data test2;
input id@;
do time= 1 to 3; /*对于每个id，都有time从1到3取值*/
input score@@; /*对于每个id与time的组合，都有一个score相对应*/
output;
end;
cards;
1 86 89 90
2 89 87 91
...
;
proc print;
run;
```
````

#### 1.5.5. 产生新变量

```sas
新变量 = 表达式 或者 函数
```

| 算术运算符      | 比较运算符                                                   | 逻辑运算符              |
| ---------- | ------------------------------------------------------- | ------------------ |
| + （加）      | =（等于）、 ^=（不等于）                                          | &或and（表示2个表达式同时成立） |
| - （减）      | >（大于）、 <（小于）                                            | \|或or（表示2个表达式任一成立） |
| \* （乘）     | >=（大于等于）、 <=（小于等于）                                      |                    |
| / （除）      | in(范围)（属于其中）grade in(2,4,6)表示只要是grade为2、4、6中的其中一个就算符合条件 |                    |
| \*\* （幂次方） | dept not in(“A”, “B”)表示只要dept不是“A”或“B”就算成立              |                    |

**1.5.5.1. 宽度限制**

产生新变量前，对其进行宽度限定，使用length

```sas
data bonus;
input id g1 g2 bonus;
length g $6.;  /*定义字符变量g的长度为6，若无该语句，则宽度受第一个值的影响*/
if g1>=70 and g2>=70 then g="合格";
else g="不合格";
if g="合格" then bonus=bonus*1;
else bonus=bonus*0.5;
cards;
1 67 74 10000
2 80 85 11000
3 89 87 11000
4 78 69 10000
;
proc print;
run;
```

无length语句：

| Obs | id | g1 | g2 | bonus | g  |
| --- | -- | -- | -- | ----- | -- |
| 1   | 1  | 67 | 74 | 5000  | 不合 |
| 2   | 2  | 80 | 85 | 11000 | 合格 |
| 3   | 3  | 89 | 87 | 11000 | 合格 |
| 4   | 4  | 78 | 69 | 5000  | 不合 |

有length语句：

| Obs | id | g1 | g2 | bonus | g   |
| --- | -- | -- | -- | ----- | --- |
| 1   | 1  | 67 | 74 | 5000  | 不合格 |
| 2   | 2  | 80 | 85 | 11000 | 合格  |
| 3   | 3  | 89 | 87 | 11000 | 合格  |
| 4   | 4  | 78 | 69 | 5000  | 不合格 |

### 1.6. 数据集的导入和导出

### 1.7. 建立和调用永久性数据集

SAS数据都是存放在资源管理器的逻辑库中。逻辑库中有SAS自带的6个文件夹，用来存放不同类型的数据。 理论上，我们在用data语句起名字时，还需要告诉SAS建立的数据集是放在哪个文件夹里。如，data sasuser.fh;表示建立数据集fh，放在sasuser这个文件夹，再如data work.fh;表示建立数据集fh，放在work这个文件夹。 SAS默认，如果data语句中没有加文件夹名字，就是**默认为存放到临时文件夹work**中，也就是说，data fh;与data work.fh;是完全等同的。当关闭sas时work内的数据集将清空。

#### 1.7.1. 建立永久性数据集

相当于把建立的数据集存放到硬盘的一个文件夹中。具体主要包括三个步骤：

* 在电脑硬盘建立一个文件夹，准备用来存放数据集；
* 在SAS的资源管理器中也新建一个文件夹，起个自己喜欢的英文名字；
* 把这两个文件夹关联起来。

**方式一：菜单中新建** \[1]在本地硬盘建立一个文件夹作为数据集存放处（假设路径为g盘目录下的mysasfile文件夹） \[2]逻辑库处右键新建新库并命名（假设是mydir），路径浏览选择上述文件夹 \[3]编辑器处添加数据集

```sas
data mydir.test; /*在mydir库中添加名为test的新数据集*/
input id day: yymmdd8. city:$20.;    
format day yymmddp10.;  
cards;
370685 980515 shandong
;
run; /*运行后，在g盘目录下的mysasfile文件夹中会出现test数据集*/
```

**方式二：编辑器中新建，并与逻辑库关联** \[1]同上 \[2]编辑器中新建新库，并添加数据集

```sas
libname mydir "g:\mysasfile"; /*新建名为mydir的新库，并与路径中的文件夹相关联*/
data mydir.test; /*在mydir库中添加名为test的新数据集*/
input id day: yymmdd8. city:$20.;    
format day yymmddp10.;  
cards;
370685 980515 shandong
;
run; /*运行后，在g盘目录下的mysasfile文件夹中会出现test数据集*/
```

**方式三：编辑器中新建**

```sas
data "g:\mysasfile\first";/*在g:\mysasfile\中新建一个名为first的数据集*/
input id day: yymmdd8. city:$20.;    
format day yymmddp10.;  
cards;
370685 980515 shandong
;
run;
```

#### 1.7.2. 调用永久性数据集

调用硬盘上的SAS数据集 \[1]首先要知道硬盘上的文件夹位置及名称 \[2]]利用libname语句读取相应位置的文件夹中的文件

```sas
libname mydir "g:\mysasfile"; 
proc print data= mydir.test;
run;
```
