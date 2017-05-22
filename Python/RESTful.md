# RESTful的架构风格

1. GET  --> (SELECT) : 从服务器取出资源
2. POST --> (CREATE) : 在服务器新建资源
3. PUT  --> (UPDATE) : 在服务器更新资源（全量）
4. PATCH -> (UPDATE) : 在服务器更新资源（局部）
5. DELETE > (DELETE) : 从服务器删除资源

## 无状态

所有的资源，都可以通过URI定位，这个定位和其他资源无关，不会因为其他资源的变化而变化。
