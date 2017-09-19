# 常用Oracle分析函数详解

## 1. 基本概念理解

####1.1分析函数

分析函数是在主查询结果的基础上进行一定的分析,如分部门汇总,分部门求值等.

#### 1.2数据窗口

1.2.1 Oracle分析函数建立在所谓的数据窗口之上,数据窗口可以理解为一个数据集合.主查询的数据可以按照不同的标准分割成不同的数据集.比如 partition by manager_id

按照manager_id将主查询的数据分成N个不同的数据窗口

1.2.2 其次,数据窗口内部还可以通过order by实现一定的顺序

####1.3分析函数和group by的区别和联系

1.3.1 分析函数的功能大部分都可以通过group by来聚合完成

1.3.2 分析函数查询出来的行是由主查询决定的,group by的行数结果是由group by后面的集合构成的唯一性决定的,通常比主查询的结果要少.

#### 1.3 示例

```sql
select sum(emp.salary) over(partition by emp.manager_id) sum_salary_department from fwk_tbx_employees emp order by emp.salary desc
```

查询结果:

当前行对应人员所在部门的薪水总额

avg ,count与之类似

过程理解:

- 首先将查询出来的数据集按照manager_id分割
- 查找当前行的manager_id对应的数据集
- 对以上数据集合求和,生成一个结果附在新添加的列中

```sql
dense_rank() over(partition by emp.manager_id order by emp.salary desc) rank_salary_dept
--该部门薪水排行
```

当前行对应人员在所在部门的薪水排名(不出现并列情况,相同的值也会有不同的排序,且排序连续) 

rank函数与之相反,要出现并列的情况,且并列将导致排名不连续.

过程理解:

- 首先将查询出来的数据集按照manager_id分割
- 对当前行manager_id对应的数据集进行排序
- 将本行对应的行号提取并附在附加列中







