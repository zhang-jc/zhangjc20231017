title: hive 内置字符串（String）函数
tags:
  - Hive
  - SQL
  - 内置函数
categories:
  - 大数据
  - Hive
date: 2016-05-15 08:55:09
---

Hive 支持以下内置字符串函数：

1. int ascii(string str)：返回字符串第一个字符的 ASCII 码值
2. string base64(binary bin)：将二进制参数转换为 base 64 编码的字符串

<!-- more -->

3. string concat(string|binary A, string|binary B...)：将作为参数的字符串或者二进制数据依次拼接，并返回拼接后的结果。例如：concat('foo', 'bar')，返回结果为‘foobar’。这个函数可以接受任意数量的字符串参数。
4. array<struct<string,double&gt;&gt; context_ngrams(array<array<string&gt;&gt;, array<string&gt;, int K, int pf)：给定一个字符串作“语境”，返回一组标记句子的Top-k语境n-grams。更多信息参见 [StatisticsAndDataMining](https://cwiki.apache.org/confluence/display/Hive/StatisticsAndDataMining)
5. string concat_ws(string SEP, string A, string B...)：像上面的 concat() 函数一样，不过可以自定义分隔符 SEP。
6. string concat_ws(string SEP, array<string>)：像上面的 concat_ws() 函数一样，不过传入一个字符串数据作为参数。
7. string decode(binary bin, string charset)：用提供的字符集（'US-ASCII', 'ISO-8859-1', 'UTF-8', 'UTF-16BE', 'UTF-16LE', 'UTF-16' 中的一个）解码第一个参数为一个字符串。如果任一个参数为 null，则结果也为 null。
8. binary encode(string src, string charset)：用提供的字符集（'US-ASCII', 'ISO-8859-1', 'UTF-8', 'UTF-16BE', 'UTF-16LE', 'UTF-16' 中的一个）编码第一个参数为二进制。
9. int find_in_set(string str, string strList)：返回 str 在 strList 中第一次出现的序号；strList 是一个逗号分隔的字符串。如果任何一个参数为 null，则返回 null。如果第一个参数包含逗号则返回 0。例如，find_in_set('ab', 'abc,b,ab,c,def') 返回 3。
10. string format_number(number x, int d)：格式化数字 X 为像 '#,###,###.##' 的格式，四舍五入到 D 的小数点位置，并返回结果为一个字符串。如果 D 是 0，结果没有小数点或分数部分。
11. string get_json_object(string json\_string, string path)：基于给定的 json 路径从 json 字符串中提取 json 对象，返回提取 json 对象的 json 字符串。如果输入的 json 字符串非法则返回 null。备注：json 路径只能包含 [0-9a-z_] 字符，即，没有大写或特殊字符。并且键不能以数字开头。这是由于 Hive 列名称的限制。
12. boolean n_file(string str, string filename)：如果字符串 str 在 filename 中整行出现则返回 true。
13. int instr(string str, string substr)：返回 substr 在 str 中第一次出现的位置。如果任一个参数为 null，则返回 null；如果在 str 中找不到 substr，则返回 0。注意这个函数不是以 0 为基础的。str 中第一个字符的索引是 1。
14. int length(string A)：返回字符串的长度。
15. int locate(string substr, string str[, int pos])：返回 pos 位置之后 substr 在 str 中第一次出现的位置。
16. string lower(string A) lcase(string A)： 返回全部字符转为小写之后的字符串。例如，lower('fOoBaR') 结果为 ‘foobar’。
17. string lpad(string str, int len, string pad)：左边添加 pad 参数输入的字符使字符串长度为 len，然后返回该字符串。
18. string ltrim(string A)：返回从 A 的开头（左手边）删除空白字符后的字符串。例如，ltrim(' foobar ') 结果为 ‘foobar’。
19. array<struct<string,double&gt;&gt; ngrams(array<array<string&gt;&gt;, int N, int K, int pf)：返回一组标记句子的Top-k语境n-grams，就像 sentences() UDAF 返回的结果。参见 [StatisticsAndDataMining](https://cwiki.apache.org/confluence/display/Hive/StatisticsAndDataMining) 获取更多信息。
20. string parse_url(string urlString, string partToExtract [, string keyToExtract])：返回指定的 URL 中的部分。partToExtract 合法的取值有 HOST、PATH、QUERY、REF、PROTOCOL、AUTHORITY、FILE 和 USERINFO。例如，parse_url('http://facebook.com/path1/p.php?k1=v1&k2=v2#Ref1', 'HOST') 返回 ‘facebook.com’。并且通过提供第三个参数，可以提取 QUERY 中普通的键值，例如，parse_url('http://facebook.com/path1/p.php?k1=v1&k2=v2#Ref1', 'QUERY', 'k1') 返回 ‘v1’。
21. string printf(String format, Obj... args)：返回按照输入的格式输出格式化的字符串。
22. string regexp_extract(string subject, string pattern, int index)：返回使用模式提取的字符串。例如，regexp_extract('foothebar', 'foo(.\*?)(bar)', 2) 返回 ‘bar’。注意，使用预定义字符类型有一些必要的关注：使用 ‘\s’ 作为第二个参数将匹配字母 s；匹配空白字符 ‘\\\s’ 是必须的，等。‘index’ 参数是 Java 正则 Matcher group() 方法索引。查阅 docs/api/java/util/regex/Matcher.html 获取‘index’或 Java 正则 group() 方法的更多信息。
23. string regexp_replace(string INITIAL_STRING, string PATTERN, string REPLACEMENT)：返回用 REPLACEMENT 的实例替换 INITIAL_STRING 中所有 PATTERN 中定义的匹配 java 正则表达式的子字符串的结果字符串。例如，regexp_replace("foobar", "oo|ar", "") 返回‘fb’。注意，使用预定义字符类型有一些必要的关注：使用 ‘\s’ 作为第二个参数将匹配字母 s；匹配空白字符 ‘\\\s’ 是必须的，等。
24. string repeat(string str, int n)：重复 str n 次。
25. string reverse(string A)：返回反转后的字符串。
26. string rpad(string str, int len, string pad)：右边添加 pad 参数输入的字符使字符串长度为 len，然后返回该字符串。
27. string rtrim(string A)：返回从 A 的末尾（右手边）删除空白字符后的字符串。例如，rtrim(' foobar ') 结果为 ‘foobar’。
28. array<array<string&gt;&gt; sentences(string str, string lang, string locale)：标记一个自然语言文本字符串为单词和句子，每个句子在适当的句子边界被拆分并且作为一个单词数组返回。‘lang’和‘locale’是可选参数。例如，sentences('Hello there! How are you?') 返回 ( ("Hello", "there"), ("How", "are", "you"))。
29. string space(int n)：返回 n 空白的字符串。
30. array split(string str, string pat)：从 pat 左右分解 str（ pat 是一个正则表达式）。
31. map<string,string&gt; str_to_map(text[, delimiter1, delimiter2])：使用两个分隔符将文本分割为键值对。delimiter1 分割文本为键值对，delimiter2 拆分每个键值对。默认的第一分隔符是‘,’，第二个分隔符是‘＝’。
32. string substr(string|binary A, int start) substring(string|binary A, int start)：返回从 start 位置到末尾的字符串 A 的子字符串或字节数组的部分。例如，substr('foobar', 4) 结果为 ‘bar’。
33. string substr(string|binary A, int start, int len) substring(string|binary A, int start, int len)：返回从 start 位置开始长度为 len 的 A 的字符串或字节数组的部分。例如，substr('foobar', 4, 1) 返回‘b’。
34. string substring_index(string A, string delim, int count)：返回字符串 A 中 delim 分隔符第 count 次匹配前的子字符串。如果 count 是正数，所有到左边最后分隔符（从左边计算）都会返回。如果 count 是负数，所有到右边最后分隔符（从右边计算）都会返回。当搜索 delim 时，Substring_index 是大小写敏感的。示例：substring_index('www.apache.org', '.', 2) = 'www.apache'。
35. string translate(string|char|varchar input, string|char|varchar from, string|char|varchar to)：通过用 to 中对应的字符替换 from 中展示的字符转化 input 字符串。这跟 [PostgreSQL](http://www.postgresql.org/docs/9.1/interactive/functions-string.html) 中的 **translate** 函数相似。这个 UDF 的任一个参数为 NULL，结果也为 NULL。
36. string trim(string A)：返回从 A 两端去除空白字符后的字符串。例如，trim(' foobar ') 结果为 ‘foobar’。
37. binary unbase64(string str)：转换参数从一个 base 64 字符串为 BINARY。
38. string upper(string A) ucase(string A)：返回转换字符串 A 中所有字符为大写后的字符串。例如，upper('fOoBaR') 结果为‘FOOBAR’。
39. string initcap(string A)：返回字符串，每个单词的第一个字母为大写，所有其他字母为小写。单词以空白字符分割。
40. int levenshtein(string A, string B)：返回两个字符串之间的 Levenshtein 距离。例如，levenshtein('kitten', 'sitting') 结果为 3。
41. string soundex(string A)：返回字符串的 soundex 码。例如，soundex('Miller') 结果为 M460。
