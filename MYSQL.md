613. 直线上的最近距离](https://leetcode-cn.com/problems/shortest-distance-in-a-line/)

```
select min(num) shortest
from (
    select abs(t1.x - t2.x) num
    from point t1
    left join point t2
    on t1.x != t2.x
) t
```

#### 类似题型

- ##### [182. 查找重复的电子邮箱](https://leetcode-cn.com/problems/duplicate-emails/)

    ```
    select distinct p1.Email
    from Person p1 
    left join Person p2
    on p1.Email = p2.Email 
    and p1.Id != p2.Id
    where p2.Id is not null
    ```

    

#### [627. 变更性别](https://leetcode-cn.com/problems/swap-salary/)

```
update salary 
set sex = 
(
    char(ascii("f")+ascii("m")-ascii(sex))
)
```

#### [1251. 平均售价](https://leetcode-cn.com/problems/average-selling-price/)

```
select Product.product_id,round(Product.allPrices/numsTable.nums,2) average_price
from (
    select p.product_id,sum(price*units) allPrices
    from Prices p
    left join UnitsSold u
    on p.product_id = u.product_id
    and u.purchase_date between p.start_date and p.end_date
    group by p.product_id
) Product
left join(
    select product_id,sum(units) nums
    from UnitsSold
    group by product_id
) numsTable 
on Product.product_id = numsTable.product_id


SELECT P.PRODUCT_ID, ROUND(SUM(PRICE * UNITS) / SUM(UNITS), 2) AS AVERAGE_PRICE
FROM PRICES AS P
INNER JOIN UNITSSOLD AS U
ON P.PRODUCT_ID = U.PRODUCT_ID
AND U.PURCHASE_DATE BETWEEN P.START_DATE AND P.END_DATE
GROUP BY 1;
```

- 知识点

    ```
    inner join 的连接
    group by 1 ：以第一列作为分组条件
    sum()/sum() ：分别求和
    round(,2)：保留两位
    日期 BETWEEN 日期1 AND 日期2：日期过滤    表示 日期1<= 日期 <=日期2
    ```

#### [584. 寻找用户推荐人](https://leetcode-cn.com/problems/find-customer-referee/)

```
select name
from customer
where  referee_id is null or referee_id != 2
```

- 知识点

    ```
    MySQL 使用三值逻辑 —— TRUE, FALSE 和 UNKNOWN。 任何与 NULL 值进行的比较都会与第三种值 UNKNOWN 做比较。
    ```

    

#### [620. 有趣的电影](https://leetcode-cn.com/problems/not-boring-movies/)

```
select * 
from cinema
where description != "boring"
and mod(id,2) = 1
order by rating  desc
```

- 知识点

    ```
    desc  倒序
    mod(num,x) 取余
    ```

    

#### [1179. 重新格式化部门表](https://leetcode-cn.com/problems/reformat-department-table/)

```
SELECT id,
SUM(CASE `month` WHEN 'Jan' THEN revenue END) Jan_Revenue,
SUM(CASE `month` WHEN 'Feb' THEN revenue END) Feb_Revenue,
SUM(CASE `month` WHEN 'Mar' THEN revenue END) Mar_Revenue,
SUM(CASE `month` WHEN 'Apr' THEN revenue END) Apr_Revenue,
SUM(CASE `month` WHEN 'May' THEN revenue END) May_Revenue,
SUM(CASE `month` WHEN 'Jun' THEN revenue END) Jun_Revenue,
SUM(CASE `month` WHEN 'Jul' THEN revenue END) Jul_Revenue,
SUM(CASE `month` WHEN 'Aug' THEN revenue END) Aug_Revenue,
SUM(CASE `month` WHEN 'Sep' THEN revenue END) Sep_Revenue,
SUM(CASE `month` WHEN 'Oct' THEN revenue END) Oct_Revenue,
SUM(CASE `month` WHEN 'Nov' THEN revenue END) Nov_Revenue,
SUM(CASE `month` WHEN 'Dec' THEN revenue END) Dec_Revenue
FROM Department
GROUP BY id;
```

- 构建新的表格



#### [1435. 制作会话柱状图](https://leetcode-cn.com/problems/create-a-session-bar-chart/)

```
select derived1.bin,ifnull(derived2.total,0) total from
(select 
    case
        when duration/60<5 then '[0-5>'
        when duration/60<10 then '[5-10>'
        when duration/60<15 then '[10-15>'
        else '15 or more'
    end bin,
    count(8) total
from Sessions
group by bin)derived1
# 为了解决分组丢失问题
right join (
select '[0-5>' bin union
select '[5-10>' bin union
select '[10-15>' bin union
select '15 or more' bin       
)derived2
on derived2.bin = derived1.bin
```

- 知识点

    ```
    1. 构建柱状图 ：通过select *  union
    2. 通过 left join,right join 补全缺失
    ```

#### [1211. 查询结果的质量和占比](https://leetcode-cn.com/problems/queries-quality-and-percentage/)

```
# Write your MySQL query statement below
select query_name,round(avg(rating/position),2) quality,
round(sum(if(rating<3,1,0))/count(query_name)*100,2) poor_query_percentage
from Queries    
group by query_name
```

#### [196. 删除重复的电子邮箱](https://leetcode-cn.com/problems/delete-duplicate-emails/)

```
delete p1.*
FROM Person p1
inner join  Person p2
on p1.Email = p2.Email 
where p1.Id > p2.Id
```

- 知识点

    ```
    # 最原始的delete语句
    delete from table1 where table1.id = 1;
    
    # 如果需要关联其他表进行删除
    delete table1 
    from table1 inner join table2 on table1.id = table2.id 
    where table2.type = 'something' and table1.id = 'idnums';
    ```

    

#### [1407. 排名靠前的旅行者](https://leetcode-cn.com/problems/top-travellers/)

```
select u.name,ifnull(sum(r.distance),0) travelled_distance
from Rides r
right join Users u
on r.user_id = u.id
group by r.user_id
order by travelled_distance desc,u.name asc
```

- 知识点

    ```
    部分用户参加了，部分用户没参加，导致数据缺失，使用ifnull()将null补0
    
    order  A 排序1，B 排序2
    ```

    

#### [1485. 按日期分组销售产品](https://leetcode-cn.com/problems/group-sold-products-by-the-date/)

```
SELECT
	sell_date,
	COUNT(DISTINCT product) AS num_sold,
	GROUP_CONCAT(DISTINCT product) AS products
FROM
	Activities AS a
GROUP BY
	sell_date
ORDER BY
	sell_date;
```

- 知识点

    ```
    group_concat( [DISTINCT] 要连接的字段 [Order BY 排序字段 ASC/DESC] [Separator ‘分隔符’] )
    ```

    

#### [1511. 消费者下单频率](https://leetcode-cn.com/problems/customer-order-frequency/)

```
select *
from (
    select o.customer_id,c.name
    from Orders o
    left join Customers c on o.customer_id = c.customer_id
    left join Product p on o.product_id = p.product_id
    where month(order_date) in (6,7) 
    group by o.customer_id,month(o.order_date) 
    having sum(o.quantity*p.price)>=100 
) t
group by customer_id
having count(*)>=2
```

- 知识点

    ```
    双重group by
    ```

    

#### [574. 当选者](https://leetcode-cn.com/problems/winning-candidate/)

```
select name
from Candidate
where id = (
    select 
    from Vote
    group by CandidateId
    order by count(*) desc
    limit 1
)
```

- 知识点

    ```
    数量最多的某个： 
    		group by 字段
    		order by count(*)
    		
    order使用函数		
    ```

- 类似题型

    - ##### [1355. 活动参与者](https://leetcode-cn.com/problems/activity-participants/)

        ```
        # Write your MySQL query statement below
        select activity
        from Friends
        group by activity
        having count(*) !=(
            select count(*) num 
            from Friends
            group by activity
            order by count(*) desc
            limit 1
        ) and count(*) !=(
            select count(*) num 
            from Friends
            group by activity
            order by count(*) asc
            limit 1
        )
        ```

#### [1045. 买下所有产品的客户](https://leetcode-cn.com/problems/customers-who-bought-all-products/)

```
select Customer.customer_id
from Customer
group by customer_id
having count(distinct product_key) =(
    select count(product_key)
    from Product
)
```

#### [608. 树节点](https://leetcode-cn.com/problems/tree-node/)

```
select t1.id,
case
    when sum(if(t1.p_id is null,0,1))=0 then "Root"
    when sum(if(t2.p_id is null,0,1))=0 then "Leaf"
    else "Inner"
end Type
from tree t1
left join tree t2
on t1.id = t2.p_id
group by (t1.id)
```

- 思路

    ```
    分别统计：父亲节点、孩子节点个数。
    ```

#### [570. 至少有5名直接下属的经理](https://leetcode-cn.com/problems/managers-with-at-least-5-direct-reports/)

```
select e1.Name
from Employee e1
inner join Employee e2
on e1.Id = e2.ManagerId
group by e1.Id
having count(e2.ManagerId)>=5
```

- 知识点

    ```
    inner join .... on   求交集
    ```

#### [534. 游戏玩法分析 III](https://leetcode-cn.com/problems/game-play-analysis-iii/)

```
F1:
select player_id,event_date, sum(games_played)  
over(partition by player_id order by event_date asc) games_played_so_far
from Activity
group by player_id,event_date

F2:
select t1.player_id,
       t1.event_date,
       sum(t2.games_played) games_played_so_far
from Activity t1,Activity t2
where t1.player_id=t2.player_id
  and t1.event_date>=t2.event_date
group by t1.player_id,t1.event_date;
```

- 知识点

    ```
    F1  sum() over 分组累加
    
    F2 通过left join,构建自连
    ```

    

#### [626. 换座位](https://leetcode-cn.com/problems/exchange-seats/)

```
select (
    case  
        when mod(id,2) = 1 and id = (select count(*) nums from seat) then id
        when mod(id,2) = 1 and id != (select count(*) nums from seat) then id+1
        else id-1
    end
) id,student
from seat
 order by id
```

- 思路

    ```
    通过调整id，来调整位置交换
    ```

    

#### [1398. 购买了产品 A 和产品 B 却没有购买产品 C 的顾客](https://leetcode-cn.com/problems/customers-who-bought-products-a-and-b-but-not-c/)

```
F1.
select distinct t1.customer_id,t4.customer_name
from Orders t1
inner join Orders t2
using(customer_id)
inner join Orders t3
using(customer_id)
left join Customers t4
using(customer_id)
where t1.product_name = 'A' and t2.product_name = 'B'
and t3.customer_id not in (select customer_id
from Orders  where product_name = 'C') 

F2.
select c.customer_id,customer_name
from (
    select customer_id
    from Orders
    group by customer_id
    having (
        sum(product_name = "A")>0
        and sum(product_name = "B")>0
        and sum(product_name = "C")=0
    ) 
) o
left join Customers c
on c.customer_id = o.customer_id
```

- 知识点

    ```
    买了A,买了B,买了C  ------>通过求交集
    								 ------>分组通过计算每个购买数量，进行过滤
    								 
    using()  连表查询
    没买     not in (select)
    having()   多重过滤
    ```

#### [197. 上升的温度](https://leetcode-cn.com/problems/rising-temperature/)

```
select w2.Id
from Weather w1
inner join Weather w2
on datediff(w2.RecordDate,w1.RecordDate) = 1
and w1.Temperature < w2.Temperature
```

#### [176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary/)

```
# Write your MySQL query statement below
select (
    ifnull((
        select DISTINCT Salary 
        from Employee
        order by Salary desc
        limit 1 offset 1
    ),null)
) as SecondHighestSalary
```

- 知识点

    ```
    limit 限制个数
    offset 偏移数	
    ```

    

#### [1355. 活动参与者](https://leetcode-cn.com/problems/activity-participants/)

#### 思路

```
查找统计数目的最大值，最小值
		select count(*) num 
    from Friends
    group by activity
    order by count(*) asc
    limit 1
```

#### 代码

```
# Write your MySQL query statement below
select activity
from Friends
group by activity
having count(*) !=(
    select count(*) num 
    from Friends
    group by activity
    order by count(*) desc
    limit 1
) and count(*) !=(
    select count(*) num 
    from Friends
    group by activity
    order by count(*) asc
    limit 1
)
```

#### [602. 好友申请 II ：谁有最多的好友](https://leetcode-cn.com/problems/friend-requests-ii-who-has-the-most-friends/)

#### 思路

```
两个列 混合计算
	采用union进行连接
```

#### 代码

```
select id,sum(num) num
from (
    (
        select requester_id id,count(*) num
        from request_accepted
        group by requester_id
    )
    union all(
        select accepter_id id,count(*) num
        from request_accepted
        group by accepter_id
    )
)t
group by id
order by num desc
limit 1
```

#### [1532. 最近的三笔订单](https://leetcode-cn.com/problems/the-most-recent-three-orders/)

#### 思路

```
分组+每组的前几：row_number()over(partition by ID_1 order by ID_2 desc) 
```

