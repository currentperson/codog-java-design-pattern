# 迭代器模式\(Iterator Pattern\)课后作业

1. 把 Iterable 模式变成自定义 Java 8 Stream 模式
2. 作为银行开发人员, 这次我们接到要将一批用户的流水导出到 Excel, 导出 Excel 的时候, 数据量可能很大, 不能够因为用户数据量大就导致内存溢出, 所以把全部用户放到 List 里面传给 `exportExcel(List<Object> objs)` 方法不是很合理, 需要传给 `exportExcel(Iterable<Object> objs)`\[此方法实现不需要关心\], 请自定义 Iterable 从数据库分页查询数据导出 excel 功能
3. 我们需要从远程接口查询用户的还款计划来做汇总, 还款计划是个分页 http 接口, 封装一个迭代器使得调用者不知道数据来源是 http 接口还是本地数据库
4.  陈述迭代器模式的缺点



