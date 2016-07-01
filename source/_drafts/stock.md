
stock

inventory

实际库存
- 采购入库需增加实际库存
- 下单时不操作实际库存
- 订单发货（扫描出库）时才扣减实际库存

采购库存
- 在途

可售库存
- 可根据实际库存和在途库存进行调整（按照某种算法或者手工）
- 下单并付款，扣减可售库存
- 下单未付款，扣减可售库存，但是给予一定的付款时限，比如30分钟-1个小时，超时未付款则归还库存到可售库存
- 下单并取消的，则归还库存到可售库存

业务上定义 加入购物车时 是否锁定库存


关于秒杀的
http://gold.xitu.io/entry/56e0e158816dfa0051da43b8


http://coolshell.cn/articles/6790.html

Multiversion concurrency control，MVCC 多版本并发控制
