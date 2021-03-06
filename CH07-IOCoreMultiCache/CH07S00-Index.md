# MultiCache 介绍

在程序运行时，会产生很多的数据，有时候我们需要将数据保存下来，并且在每次更新后都能实时的保存。
从而在程序重启、异常崩溃时能够不丢失或者尽可能少的丢失数据。

为了对这种动态变化的内存数据进行持久化存储，ATS 设计了 MultiCache 组件：

- 通过 mmap 技术减少在将数据写入磁盘时的数据拷贝次数（用户空间与内核空间）
- 通过哈希函数，将数据进行分片管理，每个片区使用独立的互斥锁，提升并发访问能力
- 使用 msync 系统调用，按照分片逐个刷新内存数据到磁盘
- 使用 HEAP 区域支持不定长数据的存储

MultiCache 在 HostDB 中用于对域名解析结果进行持久化缓存，并提供了并发查询解析结果的能力。

在本章中，将逐个对以下组件进行分析：

- Store & Span
- MultiCacheHeader
- MultiCacheBase
- MultiCacheHeapGC
- MultiCacheSync
- MultiCacheBlock
- MultiCache

