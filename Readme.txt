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
