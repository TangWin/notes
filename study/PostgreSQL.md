# PostgreSQL

*用来记录一些困惑和遇到的问题。*



### Boolean结果取反

在MySQL中可以通过 “!” 来取反，在postgreSQL中，可以通过 “NOT” 关键字来反转Boolean结果。



### 和Oracle的差异性

* 面向客户群体不同
  * Oracle：需要付费，对数据可靠性要求很高的企业用户
  * PostgreSQL：几部分组成，包括企业用户，社区用户，互联网新兴行业工作群体
* 开源程度不同
  * Oracle：不开源，商业公司，无法影响到产品决策
  * PostgreSQL：开源，用户可以自行扩展功能，从而实现自己的需求



### MacOS下默认路径

* 安装位置：/Library/PostgreSQL
* 启动脚本：/Library/PostgreSQL/10/scripts/runpsql.sh