## 概念

集群的资源可以被逻辑上划分成逻辑资源池 – Pools，并使用这些逻辑资源池提供不同的外部服务（如：块存储服务，RGW对象存储服务，CephFS文件系统服务）。一个 Pool 是 Ceph 中一些对象的逻辑分组，它并不表示一个连续的分区，而只是一个逻辑概念，类似于将二进制数据打了tag一样然后根据tag归类一样，它类似于 LVM 中的 Volume Group，类似于一个命名空间。RBD Image 类似于 LVM 中的 Logical Volume，RBD Image 必须且只能在一个 Pool 中，Pool 由一定数量的PG组成。

Pool作为Ceph集群中的逻辑存储池，可以对其定义不同的存储策略，包括：

其属性包括：

* * 冗余策略：
    * 拷贝 —— Replicated
    * 纠删码 —— Erasure Coded
  * 存储池分级缓存：多个存储池可以组成分级缓存存储，Ceph融合该特性融合了分级存储以及缓存的特性，将快速存储池作为缓存使用，通过数据热度统计，自动将热度较高的数据存储于快速存储池中，而将热度较低的数据存储于慢速存储池中，分级存储提供：
    * 缓存策略：
      * WriteBack —— 同时缓存读写数据，并定期将缓存池脏数据写回慢速存储池中
      * ReadOnly —— 写入数据直接写回慢速存储池中，仅将读数据缓存入快速存储池中
      * ReadForward，ReadProxy —— 只缓存写入数据，写入数据采用写回策略，而读数据直接访问慢速存储池。ReadForward 和 ReadProxy 的区别在于，ReadForward由客户端向慢速存储池发起请求并获取数据；而ReadProxy由服务端向慢速存储池发起请求获取数据后，将获取的数据转发给客户端。
    * 缓存调优：
      * 客户可以根据具体业务场景，设置“bloom“，“explicithash”，“explicitobject”，加快热度计算速度或者加强热度计算精度 —— bloom模式具有较快的计算速度，explicit\_object具有较好的计算精度，而explicit\_hash介于两者之间。
      * 客户可以根据具体场景，通过设置min\_read\_recency\_for\_promote与min\_write\_recency\_for\_promote，提升缓存命中效率。这两个参数表示读写访问在缓存池至少未命中几次后，才会被缓存入快速存储池，在对象未被缓存入快速存储池之前，读写请求将直接通过Proxy write/read 从慢速存储池中获取数据，这可以极大程度上避免全局数据扫描从而污染缓存池数据。
* * 副本数目 —— size：拷贝数描述了一个存储池冗余策略，表示Pool中采用几份拷贝对数据进行存储（注意：这里主副本也算作一份拷贝）
  * 副本最小数目—— min\__size：当至少有min\_size数量的拷贝时，数据才可以被读写_

  * PG数目 —— pg\_num：一个存储资源池中PG的数量

  * PGP数目 —— pgpnum：当增加PG数目是，Ceph只是为用户创建了新的PG，而这些PG中仍然是没有数据，且不会发生任何的数据迁移，当且仅当修改pgp\_num时，数据才会从旧的PG迁移至新的PG中

  * Crush规则 —— crush\_ruleset：指定Pool的数据存储与分布策略规则

  * 开启/关闭写入缓存 —— write\_fadvise\_dontneed：该值设置为1，表示建议写入数据不进行缓存，此时OSD让底层文件系在数据写入后，立即从系统缓存中释放写入的内容。该值设置为0，表示建议写入数据进行缓存，可能在不久将来被再次访问。

  * 存储池策略

    * nodelete：该值设置为1，禁止Pool被删除

    * nopgchange：该值设置为1，禁止改变pg数量

    * nosizechange：该值设置为1，禁止改变Pool中对象的拷贝数量

    * noscrub：该值设置为1，禁止该Pool中的pg进行scrub

    * nodeep-scrub：该值设置为1，禁止该Pool中的pg进行deep－scrub

    * 
* * 存储池分级策略

  * PG 数目

  * 
* 分级存储池

* 对象副本数目

CRUSH 规则集合
