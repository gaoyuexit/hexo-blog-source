title: 巧用多字符Char常量
date: 2015-05-07 08:03:20
tags: wtf
---

###巧用多字符Char

我们很熟悉`char`这个数据类型和它的使用方式：  

``` objc
char c = 'a';
```
这个 c 变量在 **ASCII** 编码下是 **97**  
还有一种不常见的多字符 char 的写法：  

``` objc
int i = 'AaBb';
```
这个 i 变量的值按每个 char 的 ASCII 值转 16 进制拼在一起，也就是说：  

``` objc
'AaBb'
-> '0x41'+'0x61'+'0x42'+'0x62'
-> '0x41614262' // 十进制1096893026
```

PS：这个组合方式和“大小端”有关系，上面是 i386 下的结果  
多字符的长度限度为最多 **4** 个 char

知道了这个特性，我们就可以做些坏事，比如：

``` objc
self.someButton.tag = 'SHIT';
...

if (button.tag == 'SHIT') {
    NSLog(@"I've got this shit button");
}
```

当然使用tag是很不推荐的写法，尽量不用。使用这个特性来避免些魔法数字或标记些整型数字还是极好的。
