---
layout: slide
title: Jekyll&#58; Make presentation page with reveal.js
description: A presentation slide for how to use reveal.js in Jekyll
theme: black
transition: slide
---

<section>
	{% mermaid %} 
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
	{% endmermaid %}
</section>


<section>
		{% mermaid %} 
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
    {% endmermaid %}
</section>


<section>
    {% mermaid %} 
graph TD
    B["fa:fa-twitter for peace"]
    B-->C[fa:fa-ban forbidden]
    B-->D(fa:fa-spinner);
    B-->E(A fa:fa-camera-retro perhaps?);
{% endmermaid %}
</section>


<section>
	{% mermaid %}
	gantt
        dateFormat  YYYY-MM-DD
        title Adding GANTT diagram functionality to mermaid
        section A section
        Completed task            :done,    des1, 2014-01-06,2014-01-08
        Active task               :active,  des2, 2014-01-09, 3d
        Future task               :         des3, after des2, 5d
        Future task2               :         des4, after des3, 5d
        section Critical tasks
        Completed task in the critical line :crit, done, 2014-01-06,24h
        Implement parser and jison          :crit, done, after des1, 2d
        Create tests for parser             :crit, active, 3d
        Future task in critical line        :crit, 5d
        Create tests for renderer           :2d
        Add to mermaid                      :1d
	{% endmermaid %}
</section>

<section>
	{% mermaid %}

sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?

  {% endmermaid %}
</section>

<section>
	{% mermaid %}
graph LR
A[Square Rect] -- Link text --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
  {% endmermaid %}
</section>

<section data-markdown>
		Topics ：A stream of messages belonging to a particular category is called a topic. Data is stored in topics.
Topics are split into partitions. For each topic, Kafka keeps a mini-mum of one partition. Each such partition contains messages in an immutable ordered sequence. A partition is implemented as a set of segment files of equal sizes.
		Partition ： Topics may have many partitions, so it can handle an arbitrary amount of data.
		Partition offset ： Each partitioned message has a unique sequence id called as offset.
		Replicas of partition ： Replicas are nothing but backups of a partition. Replicas are never read or write data. They are used to prevent data loss.
		Brokers ： Brokers are simple system responsible for maintaining the pub-lished data. Each broker may have zero or more partitions per topic. Assume, if there are N partitions in a topic and N number of brokers, each broker will have one partition.
Assume if there are N partitions in a topic and more than N brokers (n + m), the first N broker will have one partition and the next M broker will not have any partition for that particular topic.
Assume if there are N partitions in a topic and less than N brokers (n-m), each broker will have one or more partition sharing among them. This scenario is not recommended due to unequal load distri-bution among the broker.
		Kafka Cluster ： Kafka’s having more than one broker are called as Kafka cluster. A Kafka cluster can be expanded without downtime. These clusters are used to manage the persistence and replication of message data.

		Producers ： Producers are the publisher of messages to one or more Kafka topics. Producers send data to Kafka brokers. Every time a producer pub-lishes a message to a broker, the broker simply appends the message to the last segment file. Actually, the message will be appended to a partition. Producer can also send messages to a partition of their choice.

		Consumers ： Consumers read data from brokers. Consumers subscribes to one or more topics and consume published messages by pulling data from the brokers.

		Leader ： Leader is the node responsible for all reads and writes for the given partition. Every partition has one server acting as a leader.

		Follower ： Node which follows leader instructions are called as follower. If the leader fails, one of the follower will automatically become the new leader. A follower acts as normal consumer, pulls messages and up-dates its own data store.

</section>


<section data-markdown>
	Whole flow of the Producer

	// 1.确认数据要发送到的 topic 的 metadata 是可用的
		（如果该 partition 的 leader 存在则是可用的，如果开启权限时，client 有相应的权限），如果没有 topic 的 metadata 信息，就需要获取相应的 metadata；

	// 2.序列化 record 的 key 和 value
		（可以指定，也可以根据算法计算）；

	// 3. 获取该 record 的 partition 的值（可以指定,也可以根据算法计算）

	// 4. 向 accumulator 中追加数据

	// 5. 如果 batch 已经满了,唤醒 sender 线程发送数据
		（或者batch 的剩余空间不足以添加下一条 Record），则唤醒 sender 线程发送数据。


</section>

<!-- Just to show that markdown and html can be mixed -->
<section>
  <h4>Hi, I am</h4>
  <h3>Manu S Ajith</h3>
  <div style="width:200%;">
    <div style="float:left; width:30%;">
      <img alt="Jeroen De Meerleer" src="http://9piecesof8.com/img/manu.png" style="float: left; width:300px; height:300px;">
    </div>
    <div style="float:right; width:70%;">
      <ul style="float: left; padding-top: 4%;">
          <li>Writes code @RedPanthers</li>
          <li>Am committed to Ruby for some time,</li>
          <li>been dating Go during weekends,</li>
          <li>and recently flirting with Elixir at night</li>
      </ul>
    </div>
  </div>

</section>

<section data-markdown>
### Overview

[reveal.js](https://github.com/hakimel/reveal.js/) lets you to create
beautiful interactive slides using HTML. This presentation will show/demo you
how to use reveal.js and write your presentations in style with [Jekyll](http://jekyllrb.com/)
</section>

<section data-markdown>
### Slide Layout

reveal.js is already included as a `git submodule`, so that we can use it in our layouts.

Also a `slide.html` layout has been added into the `_layouts` folder which would be the base layout for all our slides.
</section>

<section data-markdown>
### Creating Slide

Now, in your page/post YAML front matter, use `slide` for the layout.

You can define *title*, *author*, *description* as well as the slide's *theme* and
*transition*

    --
    layout: slide
    title: Jekyll&#58; Make presentation page with reveal.js
    description: A presentation slide for how to use reveal.js in Jekyll
    theme: black
    transition: slide
    --


</section>

<section data-markdown>
### Slide

Each slide is enclosed in a `&lt;section&gt;` tag.

If you prefer markdown then use

`&lt;section data-markdown&gt;`

</section>

<section data-markdown>
Reference :

1. 官网： Apache Kafka[https://link.zhihu.com/?target=https%3A//kafka.apache.org/documentation/]     自然是最重要的，其实把官网文档完全理解了已经算是掌握得非常好了
2. JIRA列表：Kafka[https://link.zhihu.com/?target=https%3A//issues.apache.org/jira/browse/KAFKA/%3FselectedTab%3Dcom.atlassian.jira.jira-projects-plugin%3Asummary-panel] - ASF JIRA Kafka issue列表，使用关键字去搜索你碰到的实际问题
3. Kafka KIP[https://link.zhihu.com/?target=https%3A//cwiki.apache.org/confluence/display/KAFKA/Kafka%2BImprovement%2BProposals]： Kafka Improvement Proposals 可以看到最新的Kafka新功能提议及其讨论
4. Kafka设计文档：Index - Apache Kafka - Apache Software Foundation[https://link.zhihu.com/?target=https%3A//cwiki.apache.org/confluence/display/KAFKA/Index] 几乎可以找到所有的Kafka设计文档，其中关于controller和新版client的文章非常值得一看
5. StackOverflow: Newest &#x27;apache-kafka&#x27; Questions [https://link.zhihu.com/?target=http%3A//stackoverflow.com/questions/tagged/apache-kafka%3Fsort%3Dnewest%26pageSize%3D15]stackoverflow上的kafka问题区，里面多是小白问题，但有些问题也是很有深度的~~
6. IRC归档日志： IRC Logs for #apache-kafka[https://link.zhihu.com/?target=https%3A//botbot.me/freenode/apache-kafka/]IRC频道中有很多有价值的问题，有时候不比stackoverflow差
7. Confluent Blog： Confluent Blog: Apache Kafka Best Practices, Product Updates &amp; More[https://link.zhihu.com/?target=https%3A//www.confluent.io/blog/]   Kafka开发团队维护的一个技术博客，里面很多极具技术深度的文章
中文部分
1. 美团李志涛博客：apache kafka技术分享系列(目录索引) - 李志涛的专栏 - 博客频道 - CSDN.NET[https://link.zhihu.com/?target=http%3A//blog.csdn.net/lizhitao/article/details/39499283]  这是Kafka中国社区QQ群管理员李志涛的博客，里面的内容很详尽，虽然更新程度并不是很快
2. 我的博客(厚着脸皮推荐一下)：胡夕 - 博客园 [https://link.zhihu.com/?target=https%3A//www.cnblogs.com/huxi2b/] 我的博客没有像我们群主大大李志涛那样有清晰的目录结构，只是随性而为，想到什么就写什么了。有兴趣也可以看看
3. OrcHome: kafka中文教程 - OrcHome  不知道这是国内的哪位大神维护的一个Kafka问答网站，里面的内容不输李志涛的博客，有兴趣也可以看看
4. Kafka中国社区QQ群(再厚着脸皮推荐一下。。。)： 162272557， 国内最大的Kafka技术qq群，有兴趣可以加进来一起讨论
5. 还有就是一些散布在网上的其他文章了， 只是在阅读时一定注意发表时间。2015年或以前发表的多是老版本Kafka的一些特性，要注意与新版本的适配性。
</section>