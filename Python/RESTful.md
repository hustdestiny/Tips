# RESTful的架构风格

## 无状态

所有的资源，都可以通过URI定位，这个定位和其他资源无关，不会因为其他资源的变化而变化。

1. 资源
    网络上的一个实体，网络上的一个具体的信息
2. 统一接口
    1. GET  --> (SELECT) : 从服务器取出资源
    2. POST --> (CREATE) : 在服务器新建资源
    3. PUT  --> (UPDATE) : 在服务器更新资源（全量）
    4. PATCH -> (UPDATE) : 在服务器更新资源（局部）
    5. DELETE > (DELETE) : 从服务器删除资源
3. URI
    统一资源定位符，每个资源至少有个URI与之对应，最典型的就是URL
4. 无状态
    所谓的无状态，就是指所有的资源都可以通过URI定位。这个定位与其他资源无关，也不会因为其他资源的变化而变化。
    由于restful风格的服务是无状态的。认证机制尤为重要。
    认证机制解决的问题是用户是谁；权限机制解决的问题是用户是否被许可使用，修改，删除或者创建资源。（1.session auth 2.basic auth 3.token auth 4 OAuth）

    其中最好的是最后这种OAuth2.0 

5. 开发一个restful 的前置条件
    * flask
    * sqlalchemy
    * oauth2.0
    * json-serlize
    * permission system
    * config
    * log
    * test
    * deploy
    * cache
