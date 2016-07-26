# Java Collection

---

## ConcurrentHashMap does not permit null keys or values

```
Map<String, Object> map = new ConcurrentHashMap<>();
map.put("k", null); // NullPointerException
```
