---
layout: post
title: 测试Clickhouse的性能
category: 技巧
tags: 测试
keywords: Clickhouse
---
* content
{:toc}


参考:<https://clickhouse.tech/docs/en/getting-started/example-datasets/star-schema>

```
$ git clone git@github.com:vadimtk/ssb-dbgen.git
$ cd ssb-dbgen
$ make
```


```
$ ./dbgen -s 1000 -T c
$ ./dbgen -s 1000 -T l
$ ./dbgen -s 1000 -T p
$ ./dbgen -s 1000 -T s
$ ./dbgen -s 1000 -T d
```

