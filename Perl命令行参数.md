
## Perl命令行参数

Perl有很多命令行参数。通过它们, 我们有机会写出更简单的程序。在这篇文章里我们来了解一些常用的参数。

### Safety Net Options 安全网参数

在使用Perl尝试一些聪明(或stupid)的想法时, 错误难免会发生。有经验的Perl程序员常常使用三个参数来提前找到错误所在,

- -C 这个参数编译 Perl 程序但不会真正运行它。

而只是检查所有语法错误。每次修改 perl 程序之后我都会立刻使用它来找到任何语法错误。

    perl -c program.pl

- -W 它会提示你任何潜在的问题。

Perl 5.6.0之后的版本已经用use warnings; 替换了-w 。你应该使用 use warnings 因为它要比 -w 更灵活。

- -T 它把perl放到了tain模式。

在这个模式里, Perl 会质疑任何程序外传来的数据。例如,从命令行读取, 外部文件里读取或是 CGI 程序里传来的数据。

这些数据在 -T 模式里都会被 Tainted 掉。

### 命令行Perl参数：可以让短小的Perl程序运行在命令行。

- -e 可以让Perl程序在命令行上运行。

例如, 我们可以在命令行上运行 “Hello World” 程序而不用把它写入文件再运行。

    perl -e 'print "Hello World\n"'

多个 -e 也可以同时使用, 运行顺序根据它出现的位置。

    perl -e 'print "Hello ";' -e 'print "World\n"'

像所有的 Perl 程序一样, 只有程序的最后一行不需要以 ; 结尾。

- -M 可以像通常一样引用模块

     perl -MLWP::Simple -e  'getstore ("http://www.163.com/","163.html")' ##下载整个网页

`-M+`模块名和 use模块名一样

### 第三部分:隐式循环

- -n 增加了循环的功能, 使你可以一行一行来处理文件

    perl -n -e'print;' 1.txt 

这与下面的程序一样。

    while (<>) {
    print;
    }

`<>`打开命令行里的文件,一行行的读取。每一行缺省保存在`$_`。

    perl -n -e 'print "$. - $_"' file

上面的这一行可以写成


    while (<>) {
    print "$. - $_"
    }

输出当前行数`$.`和当前行`$_`。

- `-p`，和`-n`一样，但是还会打印`$_`的内容

如果想在循环的前后做些处理, 可以使用`BEGIN`或`END block`。下面的这一行计算文件里的字数。

    perl -ne 'END { print $t } @w = /(\w+)/g; $t += @w' file.txt

每一行所有匹配的字放入数组`@w`, 然后把`@w`的元素数目递加到`$t`.`END block`里的`print`最后输出文件总字数。
还有两个参数可以让这个程序变得更简单。

- -a 打开自动分离 (split)模式。

空格是缺省的分离号。输入根据分离号被分离然后放入缺省数组@F
使用-a，上面的命令可以写成这样：

    perl -ane 'END {print $x} $x += @F' file.txt ##使用了-a

- -F 把缺省的分离号改为你想要的。

例如把分离号定为非字符，上面的命令可以改为：

    perl -F'\W' -ane 'END {print $x} $x += @F' file.txt

下面通过Unix password 文件来介绍一个复杂的例子。 Unix password 是文本文件, 每一行是一个用户记录,
由冒号:分离。第 7 行是用户的登录 shell 路径。我们可以得出每一个不同 shell 路径被多少个用户使用 :

    perl -F':' -ane '$s{$F[6]}++;' \>; -e 'END { print "$_ : $s{$_}" for keys %s }' /etc/passwd

虽然现在不是一行, 但是你可以看出使用参数可以解决什么问题。

### 第四部分：Record Separators 数据分隔符

- $/ 和 $\ — 输入,输出分隔号。

- $/ 用来分隔从文件句柄里读出的数据, 缺省 $/ 分隔号是 \n , 这样每次从文件句柄里就会一行行的读取

- $\ 缺省是空字符, 用来自动加到要 print 的数据尾端。这就是为什么很多时候 print 都要在末尾加上 \n。

- $/ 和 $\ 可与 -n -p 一起使用。在命令行上相对应为 -0 (零) 和 -l ( 这是 L )。

- -0 后面可以跟一个 16 进制或8进制数值, 这个值用来付给 $/ 。

- -00 打开段落模式, -0777 打开slurp 模式 (即可以一次把整个文件读入) , 这与把 $/ 设为空字符和 undef 一样效果。

单独使用 -l 有两个效果：

第一，自动 chomp 输入分隔号；

第二，把$/ 值付给 $\ (这样 print 的时候就会自动在末尾加 \n )。

- -l 参数, 用来给每一个输出加 \n。例如

   perl -le 'print "Hello World"'

### 第五部分：原位编辑

使用已有的参数我们可以写出很有效的命令行程序。常见的Unix I/O 重定向:

    perl -pe 'some code' < input.txt >> output.txt

这个程序从 input.txt 读取数据, 然后做一些处理再输出到 output.txt. 你当然也可以把输出重定向到同一个文件里。
上面的程序可以通过 -i 参数做的更简单些。

-  -i 把源文件更名然后从这个更名的源文件里读取。

最后把处理后的数据写入源文件。如果 -i 后跟有其他字符串, 这个字符串与源文件名合成后来生成一个新的文件名。
此文件会被用来储存原始文件以免被 -i 参数覆盖。
这个例子把所有 php 字符替换为 perl :

    perl -i -pe 's/\bPHP\b/Perl/g' file.txt

程序读取文件的每一行, 然后替换字符, 处理后的数据重新写入( 即覆盖 ) 源文件。
如果不想覆盖源文件, 可以使用

    perl -i.bak -pe 's/\bPHP\b/Perl/g' file.txt

这里处理过的数据写入 file.txt , file.txt.bak 是源文件的备份。

### perl经典的例子

问题：遇到一问题：

    aaa@domain.com 2
    aaa@domain.com 111
    bbb@home.com 2222
    bbb@home.com 1

类似这种输出，我想把他们变换成下面形式：

    aaa@domain.com 113
    bbb@home.com 2223

就是将相同邮箱名称后面的数字相加。各位大侠能否给些思路如何用perl来实现。答案：

    perl -lane '$cnt{$F[0]}+=$F[1];END{print "$_\t$cnt{$_}" for keys %cnt}' urfile

如果熟悉了上面几个perl命令行参数的用法，上面的这个命令应该很好理解：
每次读取urfile的一行，由于使用了`-a`，打开自动分离 (split)模式。空格是缺省的分离号。
输入根据分离号被分离然后放入缺省数组`@F`中，以文件的第一行为例子`$F[0]`就是`aaa@domain.com`, `$F[1]`就是2,
`$cnt{$F[0]} +=$F[1]`是一个哈希, 以`$F[0]`为键,`$F[1]`为值,把相同key的数值都叠加起来.然后把文件的每一行都这样处理一次。
`END{}`就是在循环完之后再处理。里面的意思就是打印这个`%cnt`哈希。这个哈希数组的健就是邮箱名称,值就是叠加后和。

下面的是上面行命令的文本形式：

    #!/usr/bin/perl
    
    use strict;
    use warnings;

    my %hash;
    while (<>){
    chomp;
    my @array=split;
    $hash{$array[0]} +=$array[1];
    }

    END{
    foreach (keys %hash){
    print"$_\t$hash{$_}\n";
    }
    
    }

