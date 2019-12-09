---

title: Mycat的使用

meta:

  - name: description

    content: 博客文章

  - name: keywords

    content: 博客，文章

date: 2019-12-09

update_date: 2019-12-09
 
tags: 
  - Git
  - Dokcer

  - Yur

  - Blog
  - Mysql
  - Laravel
---



一、基本环境

W7 64位、Mycat1.6、MySQL8.0

二、Mycat核心配置文件配置

解压Mycat1.6，并对server.xml、schema.xml、rule.xml三个核心配置文件做相关配置，见下图

1、对server.xml设置登录的帐号密码以及逻辑库名(root账户可读写，user账户只读)；并设置sequnceHandlerType为0

![image](https://img2018.cnblogs.com/blog/682608/201907/682608-20190718112302048-1590677558.png)


2、配置schema.xml，这里的库、表、数据库连接等会在后面介绍。rule="mod-long"会在rule.xml中有相关设置

 ![image](https://img2018.cnblogs.com/blog/682608/201907/682608-20190718112621635-1121622303.png)




3、设置rule.xml，由于只设置了两台数据库，这里改为2
![image](https://img2018.cnblogs.com/blog/682608/201907/682608-20190718112840445-1634581741.png)



主键名称需要做下变更
![image](https://img2018.cnblogs.com/blog/682608/201907/682608-20190718113915927-940289715.png)




![image](https://img2018.cnblogs.com/blog/682608/201907/682608-20190718113915927-940289715.png)

 

三、执行创建库和表的命令，数据库mycat1、mycat2都需创建这两张表


```
CREATE TABLE IF NOT EXISTS `t_order` (
`order_id` int(20) NOT NULL,
`user_id` int(11) DEFAULT NULL,
`pay_mode` tinyint(4) DEFAULT NULL,
`amount` float DEFAULT NULL,
`order_date` datetime DEFAULT NULL,
PRIMARY KEY (`order_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='订单主表';
```


 



```
CREATE TABLE IF NOT EXISTS `t_order_detail` (
`od_id` int(20) NOT NULL,
`order_id` int(11) DEFAULT NULL,
`goods_id` int(11) DEFAULT NULL,
`unit_price` float DEFAULT NULL,
`qty` int(11) DEFAULT NULL,
PRIMARY KEY (`od_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='订单明细表'
```
;

 

四、重启mycat，并用mysql连接工具连接mycat

停止命令：mycat.bat stop

启动命令：mycat.bat start

连接mycat，注意默认端口是8066



五.连接mycat后，在mycat_order数据库下执行插入命令，并查看mycat1和mycat2中t_order表


```
INSERT INTO t_order(order_id, user_id, pay_mode, amount)VALUES(next value for MYCATSEQ_ORDER, '105', 1, 100);  执行多次
```






 

用mysql连接工具分别用root和user账户连接Mycat，均可查询出两张表的汇总数据，且user账户只能查询不能写，如下图





 

至此分库分表和读写分离的的功能基本实现

但是真正应用到项目中还有许多坑需要踩。