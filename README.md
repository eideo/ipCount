# ipCount
某系统QPS100万，每十分钟统计一下请求次数最多的100个IP。ip请求写到日志的话，其实就是超大文件中统计top k问题。10分钟6亿条记录，大约是10G级别，所以对于一般单机处理来讲不能一次性加载到内存计算。所以分治算法是处理这类问题的基本思想。

思路就是把大文件分割成多个可以内存处理的小文件，对每个小文件统计top k问题，最后再对所有统计结果合并得到最终的top k。

注意，这里的分割并不是随意分割的，那样最终结果显然是不对的，必须保证相同的ip记录都分割到同一个文件。那么hash算法最合适不过了，可以把相同的ip哈希到同一文件。

关于top k问题，效率高的解法是使用构造最小堆或者借助快速排序的思想，复杂度为O(nlogk)。这里更适合用最小堆，具体来说，就是先利用前k个数据构建一个固定大小k的最小堆，对之后的数据，小于堆顶不做处理，大于则替换堆顶并调整。这样，对每个文件顺序处理完之后就得到最终结果，而不需要保留每个文件的top k再归并。

