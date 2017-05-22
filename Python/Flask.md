# Flask
python中较火的web框架，插件众多，简洁优雅
Flask中使用jinja2的模板
Flask中使用Werkzeug作为网络层的核心

templates和static文件夹位于应用的子目录下

Flask(__name__)是为了Flask寻找templates和static文件夹位置

## Flask hello world
    
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def main():
    return 'hello world!'

if __name__ == "__main__":
    app.run()
```

此时localhost的5000端口开启一个服务端程序,在浏览器中打开可以看到helloworld,这已经是最简洁的服务端程序了

## mac起一个flask写的web服务器，iphone访问流程
1. 将mac 和 iPhone 连接到同一个wifi环境下
2. app.run(host='10.220.19.191', port=5000) 一定要配置这两项
3. 将防火墙什么的关了。起服务，然后就可以正常工作了
4. 有时候可能需要断一下wifi
5. iOS客户端发送post请求时form["key"]取不到对应的key值就会400错误
6. mysql中存储中文的问题
7. first_or_404会直接给客户端返回404
8. db.session.add(user) 之后user对象的id什么的就有啦
9. 不可以直接返回字典，需要jsonify()包装一下
10. 如果需要客户端Alamofire中发送json形式的参数，
那么在header中设置Content-Type="application/json"， encoding:使用JSONEncoding.default,Flask中使用request.get_json()则可以取出post的dict，
encoding:使用在URLEncoding.default,中取出request.values中取出get请求的参数
11. 从数据库中取出的数据还存在很大的编码问题，目前主要解决方案是mysql将库和表latin-->utf-8,还在数据库的配置里usr/local/mysql***/my.cnf中添加`default-charater-set = utf8`，但是还未有明显的作用。目前的解决方案是在发送给客户端的时候encode('utf-8')
12. 

## Flask 其他基础的配置

virtaulenv --no-site-packages venv
source venv/bin/activate
pip3 install flask
pip3 install SQLAlchemy
pip3 install mysql-connector-python-rf
pip3 freeze > requirements.txt

deactivate

## Flask blueprint

```
app/
    app.py
    user/
        __init__.py
        user.py
    role/
        __init__.py
        role.py
```
此时在user.py 中
```python
from flask import Blueprint
user = Blueprint('user', __name__, url_prefix='/user')

```
或者在role.py中
```python
from flask import Blueprint
role = Blueprint('role', __name__)

```

最后在app.py中
```python
from flask import Blueprint
from user.user import user
from role.role import role

app.register_blueprint(user)
app.register_blueprint(role, url_prefix="/role")

```
## Flask route
route 可以指定methods参数为['POST','GET','DELETE']
route 可以加多个，使得不同的路径指向同一个视图处理函数
route endpoint参数，每一个url--->endpoint--->func

## Flask jsonify
包裹普通的字典或者是数组对象，使之成为响应
return jsonify({"key": "value"})

## Flask request
判断出请求的类型，request.method == 'POST'
取出post的数据    request.form["key"]

## Flask make_response

设置cookie 
res = make_response("")
res.set_cookie("key", "value")


## Extension

1. flask-sqlalchemy
2. flask-restful
3. flask-oauth
4. flask-babel
5. flask-wtf
6. flask-login
7. flask-mail





