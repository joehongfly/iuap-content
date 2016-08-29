# 后台任务服务端组件概述 #

## 业务需求 ##

应用程序中经常会用到跑定时任务的需求，比如定时垃圾回收。有关任务调度需求有时候很复杂，如每隔多长时间重复执行，一个任务在不同时间段执行等。

##解决方案##

本组件提供对Quartz的封装，提供独立的任务调度服务。避免业务系统直接配置Quartz的带来的性能消耗和集群环境的并发问题。 业务系统通过Rest服务添加任务，任务调动执行时回调业务系统的URL启动任务。

iuap-dispatch-service组件功能包括添加、删除、暂停、重启任务。不仅提供了外部调用的Rest服务，并且组件本身也有完整的任务配置界面，包括任务调度，日志查询等功能。

## 功能说明 ##
1.	提供独立的任务调度服务；
2.	支持定时任务执行；
3.	支持重复任务执行；
4.	提供Rest服务对任务进行操作，包括，添加、删除、暂停、重启任务等；
5.	支持集群环境下任务执行唯一；
6.	提供任务配置与管理API和界面；
7.	任务支持分组管理；
8.	支持任务日志查询和搜索；


# 整体设计 #

## 依赖环境 ##

组件采用Maven进行编译和打包发布，依赖Quartz框架,其对外提供的依赖方式如下：
```
	<dependency>
	  <groupId>com.yonyou.iuap</groupId>
	  <artifactId>iuap-dispatch-service</artifactId>
	  <version>${iuap.modules.version}</version>
	</dependency>
```

${iuap.modules.version} 为平台在maven私服上发布的组件的version。

## 功能结构 ##

<img src="images/service.jpg"/>


## 功能说明 ##

iuap-dispatch-service组件功能包括添加、删除、暂停、重启任务。不仅提供了外部调用的Rest服务，并且组件本身也有完整的任务配置界面，包括任务调度，日志查询等功能。

# 使用说明 #

## 组件包说明 ##

iuap-dispatch-service组件功能包括添加、删除、暂停、重启任务。不仅提供了外部调用的Rest服务，并且组件本身也有完整的任务配置界面，包括任务调度，日志查询等功能。

##组件配置##

**1:在属性文件中，配置数据库连接等信息**
dispatch_dbinfo.properties如下：
```
    jdbc.driverClassName=com.mysql.jdbc.Driver
	jdbc.url=jdbc:my`://IP:PORT/DATABASE?useUnicode=true&characterEncoding=utf-8
	jdbc.username=用户名
	jdbc.password=密码
```

**2:执行数据库脚本，预置数据库表信息**


依次执行examples项目下sql目录中的dll.sql、index.sql、dml.sql建立数据库并初始化数据。

预置数据库表dispatch_taskway的信息，这张表是用户要执行任务的清单，需要用户预置进去，其中url是指你要执行的定时任务，通过HTTP的方式访问。
 

## 工程样例 ##

组件提供示例工程可从maven库上下载。

## 组件使用说明 ##

任务调度提供两种方式如下：

### 通过Rest任务调度 ###

* 新建任务Rest接口*

 ** A、新增一个简单任务**

*(1)Rest服务URL*
   "http://localhost:8080/iuap-dispatch-service/dispatchserver/add.do"
*(2)参数,格式如下*

```
{"replace":true,"recallConfig":{"data":{},"option":{"url":"http://localhost:8080/iuap-dispatch-service/dispatchserver/pause.do"},"recallType":"HTTP"},"taskConfig":{"triggerType":"SimpleTrigger","jobCode":"22b511e8-1b80-4f4d-b65e-48f52d8aa682","groupCode":"simpleTaskGroup","startDate":1463813876403,"endDate":null,"priority":0,"timeConfig":{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}},"note":"note"};
```

其中

*替换属性：*
"replace" 指是否替换已有的同名任务。 note为调用者自定义的参数

*回调配置：* 指回调参数、URL地址以及回调类型，目前只支持HTTP类型

{"data":{},"option":{"url":"*http://localhost:8080/iuap-dispatch-service/dispatchserver/pause.do*"},"recallType":"HTTP"}

"url":http://localhost:8080/iuap-dispatch-service/dispatchserver/pause.do
这个是示例，要传入回调的URL

*时间配置：*{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}

*任务配置（集成了时间配置）*：      {"triggerType":"SimpleTrigger","jobCode":"666f35fc-387e-41c6-96e0-146acd5541a6","groupCode":"simpleTaskGroup","startDate":1464065198495,"endDate":null,"priority":0,"timeConfig":{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}} 


 ** B、新增一个Cron任务**
 *(1)Rest服务URL*
   *(1)Rest服务URL*
   "http://localhost:8080/iuap-dispatch-service/dispatchserver/add.do"
* (2)参数,格式如下*

```
 "{\"replace\":true, \"recallConfig\":{\"data\":{\"serverName\":\"Windows 2003\"},\"option\":{\"url\":\"http://localhost:8080/iuap-dispatch-service/dispatchserver/pause.do\"},\"recallType\":\"HTTP\"}, \"taskConfig\":{\"cronExpress\":\"* */1 * * * ?\",\"groupCode\":\"cronTaskGroup\",\"jobCode\":\"cronTask\",\"priority\":0,\"triggerType\":\"CronTrigger\"}}";
```
其中

*替换属性：*
"replace" 指是否替换已有的同名任务。 note为调用者自定义的参数

*回调配置：* 指回调参数、URL地址以及回调类型，目前只支持HTTP类型

{"data":{},"option":{"url":"*http://localhost:8080/iuap-dispatch-service/dispatchserver/pause.do*"},"recallType":"HTTP"}
 
    
*任务配置：*
 " \"taskConfig\":{\"cronExpress\":\"* */1 * * * ?\",\"groupCode\":\"cronTaskGroup\",\"jobCode\":\"cronTask\",\"priority\":0,\"triggerType\":\"CronTrigger\"};
 
### 通过界面进行任务调度 ###
将组件war包部署到服务器上访问首页http://IP:PORT/iuap-dispatch-service进行任务的添加，添加任务界面如下图所示：

![](./images/addtask.jpg)

界面说明如下：

- 任务编码：任务的标识
- 任务名称：具有识别意思的名字
- 任务分组：指定任务所属的组
- 定时规则：任务具体执行时间，每多少天执行一次，并指定开始时间
- 调用规则：指定回调的任务，此列表的值需要用户预置进数据库表dispatch_taskway中，保存的是要执行任务的清单，其中url字段是指要执行的定时任务，通过HTTP的方式访问。
- 描述信息：对任务的简单描述


## 开发步骤 ##
- 从maven库上下载war包。
- 配置dispatch_dbinfo.properties 配置文件，连接mysql数据库的数据源信息。
- 执行dispatch.sql 和tables_mysql.sql 初始化数据库的脚本。
- 预置数据库表dispatch_taskway的信息，这张表是用户要执行任务的清单，需要用户预置进去，其中url是指你要执行的定时任务，通过HTTP的方式访问。如果不需要带界面的任务调度服务可以忽略此项。
- Rest服务调用接口提供任务的增删等功能，具体调用方式参考工程样例章节。
- 带界面的任务调度系统访问方式
http://IP:PORT/iuap-dispatch-service
 

## 常用接口 ##
- DispatchServerController
- 
<table style="border-collapse:collapse">
	<thead>
		<tr>
			<th>方法名</th>
			<th>参数</th>
			<th>返回值</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>addTask(HttpServletRequest request)</td>
			<td>httprequest请求，传入date数据，具体参考样例工程</td>
			<td>Map<String,Object>,成功失败以及成功失败信息</td>
			<td>添加任务，调用方式http://IP:PORT/iuap-dispatch-service/dispatchserver/add.do</td>
		</tr>
		<tr>
			<td>pauseTask(HttpServletRequest request)</td>
			<td>httprequest请求，传入date数据，具体参考样例工程</td>
			<td>Map<String,Object>,成功失败以及成功失败信息</td>
			<td>暂停任务，调用方式http://IP:PORT/iuap-dispatch-service/dispatchserver/pause.do</td>
		</tr>
		<tr>
			<td>resumeTask(HttpServletRequest request)</td>
			<td>httprequest请求，传入date数据，具体参考样例工程</td>
			<td>Map<String,Object>,成功失败以及成功失败信息</td>
			<td>重启任务，调用方式http://IP:PORT/iuap-dispatch-service/dispatchserver/resume.do</td>
		</tr>
		<tr>
			<td>deleteTask(HttpServletRequest request)</td>
			<td>httprequest请求，传入date数据，具体参考样例工程</td>
			<td>Map<String,Object>,成功失败以及成功失败信息</td>
			<td>删除任务，调用方式http://IP:PORT/iuap-dispatch-service/dispatchserver/delete.do</td>
		</tr>
	</tbody>
</table>


 
