---
layout: post
---

在relational model出现以前，数据库都是以hierarchical model和network model来看待数据的。1970s年代，Codd提出了relational model，于是DBMS产业就渐渐由RDBMS占领了。甚至现在只要提到DBMS，人们就默认你是在说RDBMS。

在RDBMS风行的早期，先驱者是IBM和UC Berkeley。IBM的DB2现在还在有的领域被广泛采用，而Berkeley的PostgreSQL也渐渐在互联网行业流行起来。在我刚毕业的时候，基本上无论招聘还是简历上写的最多的都是精通mysql，但现在已经开始有了有PostgreSQL的使用经验。

数据库这个行业，在几十年前其市场估值就已经到达了multibillion dollars了。这个行业真的很有搞头。但我身边有的同事却不愿意成为一名优秀的dba，我也不懂为什么。就算写再多的业务代码，也不一定比搞数据库有意思吧？况且业务有时真的很烦人，但是通过写业务代码来生存的人的数量应该是非常庞大的，因为门槛更低。写代码也是有食物链的吧。

table这个词，对于non-native speaker而言，也许难以领会其真谛。以下是它在字典里的定义：

> An arrangement of words, numbers, or signs ..., as in parallel columns, to exhibit a set of facts or relations in a definite, compact and comprehensive form.

这么严谨的定义，抄下来也挺费劲。主要就是关注到它被用于展示一些事实或者一些关系。

table的好处就是简洁明了，如同我老板说过，HTTP之所以流行起来也是因为它是明文的，是简洁的。table也可以被称为relation。而数据库则由relations组成。有了这些table，就要去思考怎么方便查询里面的记录了。我们不仅需要查到单个记录，还要查到一些有关系的记录或则满足条件的多条记录，这就对DBMS提出了更高的要求。

这些查询和操作数据的指令，用SQL来表示。SQL作为一门DSL，很好地简化了用户的操作。在我们的日常工作中，似乎所用过的DSL也就只有SQL了。简单的使用数据库自然不需要知道db engine是如何处理SQL语句的，但高级用户就要去了解了。

relation由schema和instance组成。schema就是header，而instance就是table。header定义了field已经每个field的domain，domain由domain name来表示。domain name就是“string、integer、real”这些。但schema在数据库实际的使用中，却有另外一个含义，不是header的意思，容易搞混。domain指定的就是这个field有一个固定的a set of associated values。就和我们在数学里描述有理数域、复数域一样。

tuples、records、rows都是一样的意思。

update语句可以使用选中record的field value来计算得到目标值，再设置：

```sql
update students s
set s.age=s.age+1,s.gpa=s.gpa-1
where s.sid=53688
```

现在的数据库很多都支持upsert操作了。


## IC （integrity constraints）

除了domain constraints以外，还有以下constraints：

1. key constraints
2. foreign key constraints
3. general constraits

superkey, which is a set of fields that contains a key.

> No subset of the set of fields in a key is a unique identifier for a tuple.

也就是key一定是能够用来区分记录的最少的fields。比如当sid已经能够区分（identify）每条学生记录了，那{sid, name}就是多余的，它不能成为key，只能被称作是superkey。

## drop table

删除表一定要指定restrict或cascade：

```sql
drop table students restrict
drop table students cascade
```

restrict表示如果有其它表引用了students，那么就会删除失败。cascade表示如果其它表引用了students表的数据，那么连带地删除掉。

平时我们都是drop table students而已，这应该是省略了restrict吧？

## 外键

由于外键在实践中总是问题多多，慢慢地好像就被弃用了。但现在的orm库都是支持外键的，我很好奇一些大公司的复杂的业务是怎么设计数据库表的，他们有没有用到外键呢？

```sql
foreign key (studid) references Students
```

## ER model

还没读完ER model就来读relational model，就像是还没考完科目一就来学倒车入库一样。

终于，来到了关于数据库设计的部分。现在很多人都直接设计table。但这种做法不值得提倡，正确的做法应该是先画出ER model，再翻译成a collection of tables（即a relational database schema）。

relational model相关的技巧虽然更接近于SQL，但有时我们还真的想要远离一下SQL，使我们能够一览众山小。

### Key Constraints

即one-to-many，many-to-many，one-to-one关系。

用箭头表示。（箭头是determine的意思，指向关系。关系用菱形表示。总的意思就是一个entity可以决定relationship sets中的一个relationship，这就是one。如果关系的另外一头也是箭头，那就是one-to-one，否则，就是one-to-many。如果一个箭头都没有，就说明了entity可以参与到多个关系中，那就是many-to-many）

### Participation Constraints

除了Key Constraints以外，还有Participation Constraints。后者表示一个entity set是否完全参与某个关系。是total还是partial。

用粗线表示。
