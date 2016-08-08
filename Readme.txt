********************************
* @Author	qinhj			   *
* @Brief	portable-1.2.10    *
********************************

1.  编译
mvn clean package install -Dmaven.test.skip=true

2.  bug修复
(1) jar包中不含缓存数据时, 加载辞典失败
(2) jar包中不含缓存数据时, 缓存数据失败

********************************
* @Author	qinhj			   *
* @Brief	IDEA Maven project *
********************************

1.  Junit
F4 -> Modules -> HanLP-portable -> Dependencies -> download: junit:junit:3.8.1

2.  Compile
mvn clean package install

********************************
* @Author	qinhj			   *
* @Brief	hanlp analysis     *
********************************

0.	Bugs

(1)	seg/common/Vertex.java
-	public static Vertex E = new Vertex(Predefine.TAG_END, " ", new CoreDictionary.Attribute(Nature.begin, Predefine.MAX_FREQUENCY / 10), CoreDictionary.getWordID(Predefine.TAG_END));
+	public static Vertex E = new Vertex(Predefine.TAG_END, " ", new CoreDictionary.Attribute(Nature.end, Predefine.MAX_FREQUENCY / 10), CoreDictionary.getWordID(Predefine.TAG_END));

1.	分词器

1.0	utility.MathTools
(1)计算(词, 词)转移权重	// 用于dfs中计算最短路径
    public static double calculateWeight(Vertex from, Vertex to)
    {
		// 单词from出现的总频数
        int frequency = from.getAttribute().totalFrequency;
        if (frequency == 0)
        {
            frequency = 1;  // 防止发生除零错误
        }
		// 词共现频数(基于语料库)
        int nTwoWordsFreq = CoreBiGramTableDictionary.getBiFrequency(from.wordID, to.wordID);
        double value = -Math.log(dSmoothingPara * frequency / (MAX_FREQUENCY) + (1 - dSmoothingPara) * ((1 - dTemp) * nTwoWordsFreq / frequency + dTemp));
        if (value < 0.0)
        {
            value = -value;
        }
        return value;
    }
其中:
MAX_FREQUENCY = 25146057;	// 当前总词频
dSmoothingPara = 0.1;		// 平滑参数
dTemp = (double) 1 / MAX_FREQUENCY + 0.00001;	// 平滑因子
分析: 防止除0错误处理方式不合理, 可以通过对所有词频+1平滑处理. 权值主要由共现词占前缀词的比重决定.
权值计算公式不唯一.

1.1	seg.common
(1)	Vertex	// 顶点
(2)	WordNet	// 词网
(3)	Graph	// 词图
(4)	Edge

1.2	seg.Segment
抽象类, 所有分词器的基类(分词方法是线程安全的, 但配置方法不保证).
命名实体识别, 默认由五部分构成: 人名/音译人名/日本人名/地名/机构名.
主要方法:
(1)	字符串分词
protected abstract List<Term> segSentence(char[] sentence);

1.2	seg.WordBasedGenerativeModelSegment extends Segment
抽象类, 基于词语NGram模型的分词器基类.
主要方法:
(1)	fixResultByRule	// 通过规则修正粗分词结果
将连续的数字节点合并为一个;
修正连词符词性;
分离数字中的连词符;
非标准日期字符串处理.
(2)	GenerateWord	// 合成新词网
(3)	GenerateWordNet	// 生成一元词网
(4)	speechTagging	// 词性标注

2.	分词

2.1	dictionary.CoreDictionary
(1)	Attribute
使用单个词性，默认词频1000构造

