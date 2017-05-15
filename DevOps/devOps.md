1. 首先在Amazon的[AWS](https://aws.amazon.com/cn/)上申请一个EC2，可免费一年
2. 选择Ubuntu Server 14.04 LTS 
3. ssh -i "zhouxin.pem" ubuntu@ec2-balabla.compute.amazon.com 
4. sudo apt-get install nginx supervisor python3 mysql-server
5. sudo pip3 install virtualenv
6. mkdir zhou-dev cd zhou-dev
7. virtualenv --no-site-packages venv
8. source /venv/bin/activate
9. python3 install flask 
10. *在自己的开发电脑上而不是ubuntu上装fabric*
11. 根据tar 指令打包想要打包的文件
12. put到ubuntu上然后解包
13. 在ubuntu本机上起一个flask-->app(host:127.0.0.1 port:5000)
14. 配置nginx使其指向这个app,proxy_pass  http://127.0.0.1:5000
15. 还可以配置supervisor去启动app
16. sudo supervisor reload
17. sudo supervisor start app
18. sudo supervisor status
19. sudo /etc/init.d/nginx reload

