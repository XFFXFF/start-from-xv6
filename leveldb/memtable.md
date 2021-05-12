存什么？  
key value  

Key Value编码
![](http://www.grakra.com/2017/06/17/Leveldb-RTFSC/leveldb_images/leveldb_memtable_key.png)
注意上图中地key_length指的是internal key的length。如果key_length表示user_key的length也应该没有问题，毕竟type+sequence的length是固定的。  
另外type相比sequence编码在更低的地址，这一点在做internal_key的比较时是很重要的，我看到网上有资料把type和sequencede位置画反了，容易造成误解。。。  

Memtable Entry的MemTable::Add函数中生成   
```cpp
void MemTable::Add(SequenceNumber s, ValueType type, const Slice& key, const Slice& value)
```
Add的参数分别对应MemTable的sequence，type，user_key和value。type表示该操作是插入还是删除，sequence为DB系统维护，user_key和value是用户想要存储的key value。  

