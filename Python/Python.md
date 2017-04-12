# Python3，以下的所有内容均是Python3

## python 参照廖雪峰的教程搭建服务后，如有希望移动设备能够访问，必须要重启一下wifi，才能正确连上

## 基本的数据类型

1. 整型
2. 浮点型
3. 字符创
4. 布尔值
5. 空值

## 变量
## 常量 Python中是没有常量的，没有任何机制保证你不修改它，全靠自觉

## 三目运算符
``` python
v1 if condition else v2
```
其中condition 为True则是V1的值，反之为V2的值

## 面向对象编程
``` python
class BaseClass(object) {
    desciption = 'person'                       // 类属性
    def __init__(self, age, gender, name) {
        self.age = age                          // 实例属性
        self.gender = gender
        self.__name = name                      // 私有属性
    }
    
    def cry(self) {                             // 对象方法
        print('instancemethod')
    }

    @classmethod
    def foo(cls){
        print('classmethod')
    }
}

class SubClass(BaseClass) {
    def __init__(self, age, name, run) {
        super(Boy, self).__init__(self, 2, 'male', name)
        self.age = age                          //属性重载
        self.run = run                          //属性扩展
    }

    def cry(self) {                             //方法重写
        print('cry loud')
    }

    @classmethod
    def foo(cls){
        print('subclass')
    }

}

```

virtualenv --no-site-packages venv
source venv/bin/activate
deactivate

pip3 install -r requirements.txt
pip3 freeze > requirements.txt

pip3 install flask
pip3 install flask-sqlalchemy
pip3 install mysql-connector-python-rf

engine = create_engine('mysql+mysqlconnector://root:@localhost:3306/awesome')
<!--engine = create_engine('数据库种类名+连接名://用户名:密码@主机:端口号/数据库名')-->


