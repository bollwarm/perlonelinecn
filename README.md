# Perl 单行中文版 

## 主要内容

- [Perl命令行参数](Perl命令行参数.md)
- [01 文件行距](01文件行距.md)
- [04 行计数](02行计数.md)
- [03 数值计算](03数值计算.md)
- [04 字符和数组生成](04字符和数组生成.md)
- [05 文本转化和替换](05文本转化和替换.md)
- [06 有选择的打印或者删除行](06有选择的打印或者删除行.md)
- [07 正则表达式](07正则表达式.md)

## 日积月累

1、 转php的序列化格式到json: `echo 'O:8:"stdClass":4:{s:7:"has_alt";b:0;s:8:"has_ctrl";b:1;s:9:"has_shift";b:0;s:8:"key_code";i:192;}'|perl -lpe 's#.*{#{#;s#s:\d+:"([^"]+)";[bi]:([^;]+);#\1 \2;#g;'`

结果:

```
{
 has_alt 0;
 has_ctrl 1;
 has_shift 0;key_code 192;
}
```
2、perl oneline统计源码行数，去除行式注释：
lib目录下 pm文件行数

    perl  -lne  '$a++  unless /^#/ and /\/\// and  /=/; END{print $a} ' `find lib/ -type f  -name "*.pm"` 

3、基因序列去重

读入基因id的建立一个散列，然后从genome.fasta找散列中存在的基因如果存在就打印出来并存储

    perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$1}=1 if @ARGV' ids.file genome.fasta > ids.fasta ids.file

## 来源

* <https://github.com/bollwarm/perlonelinecn>
* <http://ijz.me/?tag=perl-one-liners>
* <http://www.catonmat.net/download/perl1line.txt>
* <http://www.catonmat.net/blog/perl-book/>
* <http://www.perl.com/pub/a/2004/08/09/commandline.html>


