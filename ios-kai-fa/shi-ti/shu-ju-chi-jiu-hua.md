# 数据持久化

##### 1、数据持久化的几个方案？

（参考前面总结）

plist,存储字典，数组比较好用

preference：偏好设置，实质也是plist

NSKeyedArchiver：归档，可以存储对象

sqlite：数据库，经常使用第三方来操作，也就是fmdb

coreData:也是数据库储存，苹果官方的

