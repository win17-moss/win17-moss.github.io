---
layout:     post
title:      MySql面试题
subtitle:   开窗函数筛选连续登录的用户
date:       2021-04-15
author:     LNG
header-img: KD.jpg
catalog: true
tags:
    - MySql
    - 开窗函数
---


# 【MYSQL】查询连续登陆N天以上的用户

前言，开窗函数是HIVE里很常用的一个函数，同样在MYSQL里也可以实现，是筛选连续问题的首选，SQL代码不难但是逻辑非常重要

![image.png](https://i.loli.net/2021/04/15/mO1AuZvlMdn2QyP.png)

## STEP 1

第一步肯定是去重，去重，因为如果一个用户一天登陆了两次，我们只能算一次的
 SELECT DISTINCT date( pay_time ) AS 日期, member_name AS id  
 
    FROM  
    
    over_function_mysql  
    
    WHERE  
    
    ( member_name IS NOT NULL AND member_name != "" AND pay_time>"0000-00-00 00:00:01" )
    
计作A
然后我还对id为空时间为空的数据做了筛选

## STEP2

用row_number() over()函数计数, 他是一个打index的函数, 就比如一个班级里的学生成绩排名，row_number() over()就是完成你排序之后的，把
名次写上去的这个步骤，或者是rank()函数之类的排名函数，具体可以看[四大排名函数](https://blog.csdn.net/shaiguchun9503/article/details/82349050)这里就不再展开

over（）里面填的是"""PARTITION BY id order by pay_time"""

Order by 很好理解我们想让日期从前到后的排列，PARTITION BY 这个函数我们就不常用了，partition by很像group by，但是有不同，比如这里用group by会报错,group by 只能是作用于他分组汇总的列，PARTITION BY可以在保证其他列不变的前提下
SELECT
		*,
		row_number() over (  PARTITION BY id ORDER BY 日期 ) AS cum 
	FROM
	( A )  

这张表计作 B
以ID分组，以日期排序

## STEP3
用日期减去 cum 也就是我每一个用户的排序，下面用一个例子解释他为什么可以生效比如2021-1-1，A，1
    2021-1-2，A，2
cum 是每个用户的按日期从大到小的排名如果是连续的
  rank      cum = 我们要groupby的值result
2021-1-1  -  1  = 20210100
2021-1-2  -  2  = 20210100

可以看出来如果日期是连续的，这个result我们group之后sum就是2， 如果不是连续的，这个result值是不一样的SELECT
		*,
		DATE(日期)- cum AS result 
	FROM（ B ）
    
这个表计作 C
## STEP4
Group By id 和 Result,然后通过having函数筛选需要的连续天数，这里不能用where注意SELECT
	id,
	count(*) 
from ( C )
GROUP BY
	id,
	result 
HAVING
	count(*)>= 2;
# 大于等于2 筛选就是连续登陆两天以上的用户
![image.png](https://i.loli.net/2021/04/15/YMy1D6BftAaTojL.png)

后附完整代码


```python
SELECT  

	id,  

	count(*)  

FROM  

	(  

	SELECT  

		*,  

		DATE(日期)- cum AS result  

	FROM  

		(  

		SELECT  

			*,  

			row_number() over ( PARTITION BY id ORDER BY 日期 ) AS cum  

		FROM  

			( SELECT DISTINCT date( pay_time ) AS 日期, member_name AS id FROM over_function_mysql WHERE ( member_name IS NOT NULL AND member_name != "" AND pay_time > "0000-00-00 00:00:01" )) a  

		) b   

	) C   

GROUP BY  

	id,  

	result  

HAVING  

	count(*)>= 2
```
