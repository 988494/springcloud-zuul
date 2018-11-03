# Zuul 控制网关
## 配置：
	windows中配置host文件中的域名解析，在C:\Windows\System32\drivers\etc\HOSTS中添加如下配置<br>
	127.0.0.1 eureka7001.com<br>
	127.0.0.1 eureka7002.com<br>
	127.0.0.1 eureka7003.com<br>
  127.0.0.1 myzuul.com<br>
## 数据库脚本：
```clouddb01```:
DROP TABLE IF EXISTS `dept`;<br>
CREATE TABLE `dept` (<br>
  `deptno` bigint(20) NOT NULL AUTO_INCREMENT,<br>
  `dname` varchar(60) DEFAULT NULL,<br>
  `db_source` varchar(60) DEFAULT NULL,<br>
  PRIMARY KEY (`deptno`)<br>
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;<br>
<br>

## 启动顺序：<br>
	1.首先启动eureka集群：microservicecloud-eureka-7001、microservicecloud-eureka-7002、microservicecloud-eureka-7003<br>
	2.然后启动带有熔断功能得服务提供者microservicecloud-provider-dept-8001
  3.最后启动zuul控制网关：microservicecloud-provider-dept-hystrix-8001
	其中microservicecloud为所有服模块的父工程，microservicecloud-api是公共模块<br>
	其他微服务不用管<br>
	<br>
## 结果呈现：
	访问:<br>
    先访问：http://myzuul.com:9527/yangzhengxing/microservicecloud-dept/dept/get/1，出现404则表示microservicecloud-dept忽略成功<br>
    再访问：http://myzuul.com:9527/yangzhengxing/mydept/dept/get/1，有数据，表示zuul配置成功
   
## 注意:
 zuul: <br>
  #访问公共前缀<br>
  prefix: /yangzhengxing<br>
  ignored-services: "*"  #microservicecloud-dept表示：忽略微服务id为microservicecloud-dept的微服务，使得http://myzuul.com:9527/microservicecloud-dept/dept/get/1不能访问，<br>
  #只能通过http://myzuul.com:9527/mydept/dept/get/1这个访问呢！;<br>
  #"*"表示：批量忽略微服务，即忽略所有微服务，推荐使用<br>
  routes:<br> 
    mydept123.serviceId: microservicecloud-dept #将id为microservicecloud-dept的微服务路径映射为 /mydept/**，列子：http://myzuul.com:9527/microservicecloud-dept/dept/get/1映射成http://myzuul.com:9527/mydept/dept/get/1<br>
    mydept123.path: /mydept/**<br>
  <br>
	eureka服务注册中心：http://eureka7001.com:7001、http://eureka7002.com:7002、http://eureka7003.com:7003<br><br>
