# SQLAlchemy

python中较火的ORM框架，封装了较多的数据库操作

## 这里先讲一下需要使用的flask-sqlalchemy

flask-sqlalchemy是对SQLAlchemy的封装，使其更方便的在flask中使用

1. 首先是导入

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
db = SQLAlchemy(app) //或者 db.init_app(app)

class User
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True)
    email = db.Column(db.Stirng(120), unique=True)

    def __init__(self, username, email):
        self.username = username
        self.email = email

    def __repr__(self):
        return '<User %r>' % self.username
```

2. 创建表, 创建对象，添加记录
```python
from yourapplication import db
db.create_all()

from yourapplication import User
admin = User('admin', 'admin@example.com')
guest = User('guest', 'admin@example.com')

db.session.add(admin)
db.session.add(guest)
db.session.commit()
```

3. 查询

```python
users = User.query.all()

```

4. 一对多关系
```python
from datetime import datetime

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(80))
    body = db.Column(db.Text)
    pub_date = db.Column(db.DateTime)

    category_id = db.Column(db.Integer, db.ForeignKey('category.id'))
    category = db.relationship('Category', backref=db.backref('posts', lazy='dynamic'))

    def __init__(self, title, body, category, pub_date=None):
        self.title = title
        self.body = body
        if pub_date is None:
            pub_date = datetime.utcnow()
        self.pub_date = pub_date
        self.category = category

    def __repr__(self):
        return '<Post %r>' % self.title

class Category(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50))

    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return '<Category %r>' % self.name
```

5. flask-sqlalchemy的配置

```python
SQLALCHEMY_DATABASE_URI
SQLALCHMEY_BINDS
SQLALCHEMY_ECHO
SQLALCHEMY_RECORD_QUERIES
SQLALCHMEY_NATIVE_UNICODE
SQLALCHMEY_POOL_SIZE
SQLALCHEMY_POOL_TIEMOUT
SQLALCHEMY_POOL_RECYCLE
SQLALCHEMY_MAX_OVERFLOW
SQLALCHEMY_TRACK_MODIFICATIONS
```

6. column type

```Python
Integer
String(size)
Text
DateTime
Float
Boolean
PickleType
LargeBinary
```

7. 多对多关系

```python
tags = db.Table('tags',
    db.Column('tag_id', db.Integer, db.ForeignKey('tag.id')),
    db.Column('page_id', db.Integer, db.ForeignKey('page.id'))
)
// "tags"表示关联表的名字
// "tag_id"和"page_id"表示关联表中的字段名
// 'tag.id'和'page.id'表示他们外键分别是tag表和page表中的id字段，flask-sqlalchemy 会将Class名字映射成小写
```

8. 查询
peter = User.query.filter_by(username='peter').first() // 只允许一个keywordargs
users = User.query.filter(User.email.endswith('@example.com')).all() // 复杂的情况
user  = User.query.get(1) // 根据主键

9. first_or_404 或者 get_or_404

10. 多数据库
