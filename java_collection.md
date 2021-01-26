# Java Collection

---

## ConcurrentHashMap does not permit null keys or values

```
Map<String, Object> map = new ConcurrentHashMap<>();
map.put("k", null); // NullPointerException
```

`ConcurrentHashMap`使用`null`表示`value`未初始化，所以不允许`value`为`null`。

JDK1.7源码：

```
V get(Object key, int hash) {
    if (count != 0) { // read-volatile
        HashEntry<K,V> e = getFirst(hash);
        while (e != null) {
            if (e.hash == hash && key.equals(e.key)) {
                V v = e.value;
                if (v != null) // v等于null说明HashEntry未初始化完成
                    return v;
                return readValueUnderLock(e); // recheck
            }
            e = e.next;
        }
    }
    return null;
}
```

## HashMap hash
```
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

HashMap通过hashCode()的高16位和低16位异或实现hash，再`(n - 1) & hash`
计算下标，这样保证n比较小时高位也能参与到hash的计算中，同时开销也比较小。

hash数组的数量为2的幂次方，（数组大小-1）和hash值进行按位与即可快速计算hash位置。