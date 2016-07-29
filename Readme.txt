*********************************
* @Author	qinhj				*
* @Brief	HanLP 注意事项		*
*********************************

1.	辞典
自定义辞典文件结尾处无需多余空白行

2.	双数组Trie数
叶节点索引 = 父节点偏移基址

********************************
* @Author	qinhj			   *
* @Brief	Maven project info *
********************************

1. clone
git clone https://github.com/hankcs/HanLP

2. setting
F4(Open Project Structure) -> hanlp ->

Sources:
(1)	Source Folders
src/main/java
(2) Test Source Folders
src/test/java
(3) Resource Folders
src/main/resources
(4) Excluded Folders
target

Paths:
Use module compile output path:
Output path: /home/qinhj/HanLP/target/classes
Test output path: /home/qinhj/HanLP/target/test-classes

3. compile
(1) without test case
mvn clean package install -Dmaven.test.skip=true
(2) with test case
mvn clean package install

4. test
mvn clean test

*****************************
* @Author	qinhj			*
* @Brief	HanLP analysis	*
*****************************

1.	Attribute	// 核心词典中的词属性
public Nature nature[];		// 词性列表
public int frequency[];		// 词频列表
public int totalFrequency;	// 总词频

2.	CustomDictionary
    public static BinTrie<CoreDictionary.Attribute> trie;
    public static DoubleArrayTrie<CoreDictionary.Attribute> dat;
    // 动态增加(非覆盖模式)
    CustomDictionary.add("攻城狮");
    // 强行插入(覆盖模式)
    CustomDictionary.insert("白富美", "nz 1024");
    // 删除词语（注释掉试试）
    CustomDictionary.remove("攻城狮");
注意事项:
(1)	用户自定义辞典路径为字符串数组, 格式: “默认词性 路径名”.
(2)	加载辞典时, 默认第一个辞典文件为主词典文件.
(3)	加载辞典时, 若出现重复的单词, 则新属性会覆盖前一单词的属性.
(4)	根据TreeMap构建双数组trie树, 只依赖于键(key)集合, 不依赖于键值(value).
(5)	加载的多个用户自定义辞典, 缓存为dat文件时, 文件名为主辞典路径+".bin".
(6) 缓存时, 先保存-size个用户词性; 再缓存正文(单词属性: 词性/词频...), 
最后缓存双数组trie树(dat).
(7) trie树
dat:	双数组trie树, 保存用户自定义辞典数据.
trie:	二分trie树, 记录程序动态增删的单词数据.
(8)	查找单词: dat + trie
CustomDictionary.contains(String key)
(9)	添改单词:
CustomDictionary.add(...)		// 禁止添加/修改已有单词
CustomDictionary.insert(...)	// 强制添加(trie)/更新已有单词(dat)
动态强制更新后的单词信息, 并不会同步到缓存数据(.bin)中.
因为只有缓存数据不存在时, 才会在加载完辞典后, 直接缓存到磁盘.
(10)删除单词
CustomDictionary.remove(...)	// 删除单词(trie)
该方法无返回值, 且只能删除动态增加的单词(trie).
动态增删的单词保存在trie中(并不会缓存到磁盘).
动态添删/修改单词并不会更新/影响原缓存到磁盘的数据.

3.	标准分词
(1)	辞典加载顺序
com.hankcs.hanlp.dictionary.CoreDictionary
	data/dictionary/CoreNatureDictionary.txt
com.hankcs.hanlp.dictionary.CoreBiGramTableDictionary
	data/dictionary/CoreNatureDictionary.ngram.txt.table
com.hankcs.hanlp.dictionary.CustomDictionary
	data/dictionary/custom/CustomDictionary.txt
com.hankcs.hanlp.dictionary.common.CommonDictionary
	data/dictionary/person/nr.txt.value.dat
	data/dictionary/person/nr.txt.trie.dat
