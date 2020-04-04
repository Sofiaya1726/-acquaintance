# 熟人表-acquaintance
## 题目一
#### 考虑一个熟人表acquaintance（friend1，friend2，class），表示friend1和friend2是朋友，class表示类别，比如“书友”，“球友”，“酒友”等。
###### 建立数据库：
create database fs; 
###### 创建表，
表名acquaintance：
    CREAT TABLE 'FRIENDS'. 'acquaintance' ( 
    ' friend1' VARCHAR(50) CHARACTER SET utf8 COLLATE       utf8_general_ci NOT NULL, 
    ' friend2' VARCHAR(50) CHARACTER SET utf8 COLLATE       utf8_general_ci NOT NULL, 
    ' class' VARCHAR(50) CHARACTER SET utf8 COLLATE         utf8_general_ci NOT NULL)
    ENGINE = InnoDB;

###### 生成测试数据：

工具：http://www.generatedata.com

DROP TABLE acquintance;

INSERT INTO acquintance (friend1,friend2,class) VALUES ("Rogan","Price",1),("Rooney","Hamish",2),("Ivan","Lance",4),("Yuli","Fulton",3),("Raja","Damian",4),("Barclay","Nero",3)……
###### 代码未展示完全

## 题目二
##### 1. 找出互不相识的人
with t as(
select friend1 m from acquaintance
union
select friend2 from acquaintance
)
select t1.m m1, t2.m m2 
from t t1, t t2

where t1.m<>t2.m;
and not exsits(select * from acquaintance where(friend1=t1.m and friend2=t2.m) or(friend2=t1.m and friend1=t2.m));

##### 2. 找出只在一个类别出现的人
with t as (
select friend1 m, class from acquaintance
union 
select friend2, class from acquaintance
)
select m from t t1 where not exists(select * from t where m=t1.m and class<>t1.class);


##### 3. 找出在所有类别都有朋友的人
select friend1,friend2,class 
from acquaintance t1 
where not exists
 (select * from acquaintance t2 
where not exists
(select * from acquaintance where class=t2.class 
and ((friend1=t1.friend1 and friend2=t1.friend2) or (friend1=t1.friend2 and friend2=t1.friend1))));

##### 4. 找出每个类别里面朋友最多的人
with t as (
select M,class,sum(cn) as ct from 
(select friend1 AS M,class,count(*) as cn from acquaintance group by friend1,class
union all
select friend2 AS M,class,count(*) as cn from acquaintance group by M,class)
as t group by M,class)
select * from t AS t1 where ct=(select max(ct) from t where t1.class=t.class)
ORDER BY class;

##### 5.找出在同一类别里面通过朋友而结识的其他朋友
with t as 
(
select friend1 AS m1, friend2 AS m2, class from acquaintance group by friend1,friend2,class
union all
select friend2 AS m1, friend1 AS m2, class from acquaintance group by m1,m2,class
)
select a1.friend1, a1.friend2, a1.class
from acquaintance as a1
where exists
(select * from (
select t.m2 from t where a1.friend1=t.m1 and a1.class=t.class
) as f_f1,
(
select t.m2 from t where a1.friend2=t.m1 and a1.class=t.class
)as f_f2
where(f_f1.m2=f_f2.m2) and (f_f1.m2<>friend2) and (f_f2.m2<>friend1))
ORDER BY friend1;

##### 第六七题没有完成完整可行的代码，希望老师可以课上讲解一下。
