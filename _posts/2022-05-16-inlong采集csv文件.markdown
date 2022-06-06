---
layout:     post
title:      "Apache Inlong测试采集文件"
subtitle:   " -- csv文件"
date:       2022-05-16
author:     "Jie"
header-img: "img/post-bg-scala-version.jpg"
tags:
- Apache Inlong
---

部署完成开始上手，先来个本地csv到kafka
> 基于1.1版本, 跑通不容易，最开始本地csv到kafka一直没成功，经过几天调试代码熟悉，终于成功，下面步骤

# 数据分组-新建接入

![image-20220603142427727](/img/inlong/image-20220603142427727.png)

下一步添加数据源和kafka，选择json

>   调试看代码的时候发现DeserializationSchemaFactory没csv，就换成json

![image-20220606230532972](/img/inlong/image-20220606230532972.png)

流向接入kafka

![image-20220606230713046](/img/inlong/image-20220606230713046.png)

**kafka没有字段配置，需要手动数据库表stream_sink_field添加字段信息，不然输出空信息**

# 审批管理-我的审批

![image-20220529235642527](/img/inlong/image-20220529235642527.png)

# 数据分组-配置成功

![image-20220529235716714](/img/inlong/image-20220529235716714.png)

当配置成功删除pulsar自动创建的订阅，因为与flink订阅冲突

>   ```
>   org.apache.pulsar.client.api.PulsarClientException$NotAllowedException: Durable subscription with the same name already exists.
>   	at org.apache.pulsar.client.api.PulsarClientException.unwrap(PulsarClientException.java:1001)
>   	at org.apache.pulsar.client.impl.ReaderBuilderImpl.create(ReaderBuilderImpl.java:78)
>   	at org.apache.inlong.sort.flink.pulsar.PulsarUtils.createReader(PulsarUtils.java:123)
>   	at org.apache.inlong.sort.flink.pulsar.PulsarSourceFunction.run(PulsarSourceFunction.java:291)
>   	at org.apache.flink.streaming.api.operators.StreamSource.run(StreamSource.java:104)
>   	at org.apache.flink.streaming.api.operators.StreamSource.run(StreamSource.java:60)
>   	at org.apache.flink.streaming.runtime.tasks.SourceStreamTask$LegacySourceFunctionThread.run(SourceStreamTask.java:269)
>   
>   ```
>

# 测试数据

添加数据

```shell
#删除文件，貌似没监听modify
rm -rf b.csv
echo {"a":1121,"b":34} > b.csv
```

查看kafka消息，有了

![image-20220606230013989](/img/inlong/image-20220606230013989.png)

