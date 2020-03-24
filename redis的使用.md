#                       redis的使用

## 1.redis的数据类型

- 字符串

  ```nginx
  set key value
  get value
  getrangge key start end(获取value的子串，包含头尾)
  getset key value 设置一个新值，并返回旧值
  getbit key offset(将key对应的value转化为二进制（以assci码为准，对用的值），offset为向后偏移的位置，返回的值要么为1，要么为0)
  mget key key2(获取多个key对应的值)
  setex key scoend value(scoend为过期时间)
  setnx key value 只有当key不存在是才创建key
  mset key value【key1 value1】同时设置多个key value
  msetnx 整理上述两个
  psetex key milliseconds value 毫秒
  append key value
  ```

- 哈希（hash）

  ```
  HMSET KEY FILED VALUE FILED1 VALUE1
  HGET KEY FIELD
  
  //相当于string的value变为了一个map，一个key对应一个java对象，其中field为属性，value为value
  实例：HMSET wanghaofeng name wanghaofeng age 23
       HGET wanghaofeng name
  ```

- 列表（list）

  ```
  lpush key value
  lrange key start end
  LPOP key弹出最末尾的元素
  ```

- 集合（set）

  ```
  SADD KEY VALUE
  SMEMBER KEY
  ```

- 有序集合（SORTED SET）(ZSET)

  ```
  ZADD KEY SCORE MEMBER
  其中SET 使用SCORE进行排序
  ZRANGEBYSCORE key start end
  ```

  

