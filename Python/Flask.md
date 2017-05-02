# Flask
python中较火的web框架，插件众多，简洁优雅
Flask中使用jinja2的模板

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





