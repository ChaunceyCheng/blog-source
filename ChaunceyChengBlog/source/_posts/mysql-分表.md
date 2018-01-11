---
title: mysql 分表
date: 2018-01-08 17:14:23
tags: [mysql]
categories: db
---

## MERGE技术

```sql
CREATE TABLE IF NOT EXISTS `user1` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `name` varchar(50) DEFAULT NULL,  
  `sex` int(1) NOT NULL DEFAULT '0',  
  PRIMARY KEY (`id`)  
) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;  

CREATE TABLE IF NOT EXISTS `user2` (  
 `id` int(11) NOT NULL AUTO_INCREMENT,  
 `name` varchar(50) DEFAULT NULL,  
 `sex` int(1) NOT NULL DEFAULT '0',  
 PRIMARY KEY (`id`)  
) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;  

INSERT INTO `user1` (`name`, `sex`) VALUES('张映1', 0);  
INSERT INTO `user2` (`name`, `sex`) VALUES('tank', 1);

drop table alluser;
CREATE TABLE IF NOT EXISTS `alluser` (  
 `id` int(11) NOT NULL AUTO_INCREMENT,  
 `name` varchar(50) DEFAULT NULL,  
 `sex` int(1) NOT NULL DEFAULT '0',  
 INDEX(id)  
) ENGINE=MERGE UNION=(user1,user2) INSERT_METHOD=LAST AUTO_INCREMENT=1;

INSERT INTO `alluser` (`name`, `sex`) VALUES('tank4', 0);
select * from alluser;
select * from user1;
select * from user2;
```
