---
title: JMeter+Ant+Tomcat+Jenkins搭建
comments: true
toc: true
date: 2017-04-17 17:32:48
tags:
	-	testing
	-	tools
---
### 前置条件
**Java JDK 1.8**

### JMeter
**安装**：
下载[Apache JMeter binaries](http://jmeter.apache.org/download_jmeter.cgi)，解压缩即可
**配置**：
``` shell
# JMeter (/etc/profile)
export JMETER=/opt/apache-jmeter-3.1
export CLASSPTH=$JMETER/lib/ext/ApacheJMeter_core.jar:$JMETER/lib/jorphan.jar:$JMETER/lib/logkit-2.0.jar:$CLASSPTH
export PATH=$JMETER/bin:$PATH
```
**启动**：
GUI方式：``` $ jmeter ```
non-GUI方式：``` $ jmeter-n ```

<!-- more -->

### Ant
**安装**：
下载[Apache Ant  binaries](http://ant.apache.org/bindownload.cgi)，解压缩即可
**配置**：
``` shell
# ant (/etc/profile)
export ANT_HOME=/opt/apache-ant-1.10.1
export PATH=$ANT_HOME/bin:$PATH
```
**启动**：
``` $ ant ```

### JENKINS_HOME
指定jenkins主目录
注意：该配置的生效的前提是启动tomcat之前配置生效，否则建议配置在startup.sh文件最前面
**配置**：
``` shell
# jenkins (/etc/profile)
export JENKINS_HOME=/home/hnclca/.jenkins
```
### Tomcat
**安装**：
下载[Apache Tomcat  Core](http://tomcat.apache.org/download-70.cgi)，解压缩即可
不要用8.5.13版本，该版本ManagerApp界面deploy war有问题
**配置**：
1、环境变量
``` shell
# tomcat (/etc/profile)
export TOMCAT_HOME=/opt/apache-tomcat-7.0.77
export PATH=$TOMCAT_HOME/bin:$PATH
```
2、配置管理用户
``` shell
$ sudo vim /opt/apache-tomcat-7.0.77/conf/tomcat-users.xml
<role rolename="manager-gui"/>
<user username="hnclca" password="password" roles="manager-gui"/>
```
3、允许列表展示
``` shell
$ sudo vim /opt/apache-tomcat-7.0.77/conf/web.xml
<init-param>
    <param-name>listings</param-name>
    <param-value>true</param-value>
</init-param>
```
4、修改war大小上限
``` shell
$ sudo vim /opt/apache-tomcat-7.0.77/webapps/manager/WEB-INF/web.xml
<multipart-config>
    <!-- 100MB max -->  原本为50M
    <max-file-size>104857600</max-file-size>
    <max-request-size>104857600</max-request-size>
    <file-size-threshold>0</file-size-threshold>
 </multipart-config>
```
**启动与关闭**：
``` shell
$ startup.sh
$ shutdown.sh
```
**浏览网站**：
启动tomcat后，打开http://localhost:8080即可

### Jenkins
**下载**：
下载[Generic Java Package (.war)](https://jenkins.io/download/)
**部署到tomcat**：
时长比较长，要有耐心
1、打开http://localhost:8080 点击Manager App，输入管理帐户，进入管理界面
2、在Deploy->WAR file to deploy栏目中选择jenkins.war文件，点击deploy
3、完成后会进入http://localhost:8080/jenkins 要求输入管理密码，先关闭网页。
**配置**
1、将第一次启动检查网络状况时访问的网址www.google.com修改为www.baidu.com；
``` shell
$ sudo vim ~/.jenkins/updates/default.json
```
2、查看初始化的管理员密码
``` shell
$ sudo cat ~/.jenkins/secrets/initialAdminPassword
```
**启动**：
1、浏览器打开http://localhost:8080/jenkins
2、安装插件，配置帐户(如果进度条加载完后老是停在Get Started...页面，刷新网页)

### Jmeter + Ant
Ant是基于XML规范的构建工具。
**原理**：使用Ant执行两个任务，一个是执行测试用例(.jmx)生成测试结果(.jtl)，另一个是将测试结果转换为(.html)格式。
**关键点**：测试结果必须为XML格式，ant能找到ant-jmeter-XX.jar文件。
**提示**：jmeter安装目录的extras目录下不仅有ant-jmeter-XX.jar文件，同时还有ant构建脚本build.xml文件，只能执行单个测试用例，需进行一些修改。
**注意**：批量执行的多个测试用例会汇总为一份测试报告，注意测试用例中的采样器不要重名，否则测试结果会出现覆盖问题
**用例目录**：
初步设计为以下样式：
``` shell
├── resultLog
│   ├── html
│   └── jtl
└── scripts
    ├── SimpleTestPlan(jakarta).jmx
    └── SimpleTestPlan(Jmeter).jmx
```
**方案一**：
将修改后的build.xml放在测试用例下，执行命令仅用**ant**即可
缺点：每份用例都将有例重复的build.xml脚本，仅有少量目录不一样，更换用例脚本时，重复复制和修改过程
**方案二**：
将修改后的build.xml放在$JMETER_HOME/extras下(最好备份下原来的build.xml文件)，所有用例使用同一份脚本，通过命令行方式传入变量
``` shell
$ ant -buildfile /opt/apache-jmeter-3.1/extras/build.xml -Dtestpath="/home/hnclca/Documents/jmeter" -Dreport_prefix="SimpleTest"
```
### Ant+Jenkins
**创建项目**：
新建SimpleTest项目，选自由风格项目
**构建配置**：
下拉列表中选择“invoke Ant”
指定Ant的build.xml文件：
``` shell
/opt/apache-jmeter-3.1/extras/build.xml
```
指定属性参数(不能有双引号，一行一个属性)：
``` shell
testpath=/home/hnclca/Documents/jmeter
report_prefix=SimpleTest
```
其他配置如构建触发器，构建环境可自行研究配置。
**修改jmeter默认log_file产生目录**：
因为使用了jmeter目录下的build.xml文件，产生的jmeter.log在jmeter/bin文件夹下
直接构建会因权限拒绝导致构建失败
``` shell
$ sudo vim /opt/apache-jmeter-3.1/bin/jmeter.propertises
log_file=/home/hnclca/Documents/jmeter.log
```
**构建项目**：
点击左侧立即构建，左侧构建历史会显示正在构建的项目，等待构建完成之后，点击进入查看“控制台输出”可查看构建详情。

### Ant+Tomcat
将测试报告设置成Tomcat的虚拟目录，通过浏览器查看测试报告
**配置虚拟目录**：
``` shell
$ sudo vim /opt/apache-tomcat-7.0.77/conf/Cataline/localhost/testReport.xml
# 注意文件名与path要相对应
<?xml version="1.0" encoding="UTF-8" ?>
<Context path="/testReport" docBase="/home/hnclca/Documents/webapps/testReport" debug="0" privileged="true" />
```
**配置Ant构建参数**
点击Jenkis中的SimpleTest项目的配置，修改构建->高级->属性
``` shell
test.script.dir=/home/hnclca/Documents/git/SimpleTest_JmeterDemo
result.jtl.dir=${test.script.dir}/resultLog
result.html.dir=/home/hnclca/Documents/webapps/testReport
report_prefix=SimpleTest
```
**构建项目查看结果**
立即构建项目，若构建没有出错，则http://localhost:8080/testReport 中会显示测试报告列表

[SimpleTest_JmeterDemo](https://github.com/hnclca/SimpleTest_JmeterDemo)
