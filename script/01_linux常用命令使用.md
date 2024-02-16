# linux常用命令使用
---

- [curl](#curl)
- [常用写法](#常用写法)
  - [输出添加行号](#输出添加行号)
  - [行列转换](#行列转换)


---


#### curl

curl基本命令使用：[https://curl.se/docs/tutorial.html](https://curl.se/docs/tutorial.html)

curl 请求测试参数：

+ -X/--request：指定请求类型，GET/POST
+ -k/--insecure：允许不使用证书到SSL站点
+ -H/--header：指定请求头
+ -F/--form：使用multipart/form-data发送数据，通过"@+文件路径"可以实现本地文件上传
+ -b/--cookie <name=string/file>：cookie字符串或文件读取位置
+ -d/--data：使用application/x-www-form-urlencodedContent-Type发送数据，支持&合并数据和多次使用-d参数，支持使用-d @filepath的形式从文件中读取文件内容
+ --data-urlencode：使用urlencode编码后的键值对
+ `-x/--proxy <host[:port]>`：使用代理

```shell
# POST请求：application/x-www-form-urlencoded
curl https://localhost:8080/api/basic -X POST -d 'key1=value1' -d 'key2=value2' 
curl https://localhost:8080/api/basic -X POST -d 'key1=value1&key2=value2'
curl https://localhost:8080/api/basic -X POST -d @/home/user/data.txt
curl https://localhost:8080/api/basic -X POST --data-urlencode 'key1=value1&key2=value2'

# POST请求：application/json
curl https://localhost:8080/api/basic -X POST -H "Content-Type: application/json" -d '{"name": "demo", "email": "demo@example.com"}'

# POST请求：multipart/form-data
curl https://localhost:8080/api/basic -X POST -F 'key1=value1' -F 'image=@/home/user/wallpaper.jpg'

# Cookie操作：
# -b/--cookie <name=string/file>：cookie字符串或文件读取位置
curl -b cookie.txt https://localhost:8080/api/basic

# 测试网页返回值，用于测试网站是否正常
curl -o /dev/null -s -w %{http_code} www.linux.com
```

curl 文件下载：

```shell
# -o/--output：把输出写到该文件中
# -O/--remote-name：把输出写到该文件中，保留远程文件的文件名
curl https://www.demo.com/index.jsp > index.jsp
curl -O https://www.demo.com/index.jsp
curl -o index.jsp https://www.demo.com/index.jsp

# 循环下载
# 下载dodo1，dodo2，dodo3，dodo4，dodo5
curl -O http://www.linux.com/dodo[1-5].JPG

# 下载：http://www.linux.com/hello/dodo[1-5].JPG和http://www.linux.com/dd/dodo[1-5].JPG
curl -O http://www.linux.com/{hello,bb}/dodo[1-5].JPG

# 下载时重命名，#1对应{hello,bb}，#2对应dodo[1-5]
curl -o #1_#2.jpg http://www.linux.com/{hello,bb}/dodo[1-5].JPG
```



#### 常用写法

##### 输出添加行号

```shell
# 为输出添加行号，可以用在管道后面添加行号
# $0：代表整行
# NR：代表行号
awk '$0=NR":"$0' filename

# cat显示行号
cat -n filename
```

##### 行列转换

```shell
# 行转列：将\n替换成其他分隔符，sed用于替换末尾的\n
cat /etc/passwd | tr '\n' ',' | sed -e 's/,$/\n/'

# 行转列：以空格为分隔符
cat /etc/passwd | awk -F "+" '{for(i=1;i<=NF;i++) a[i,NR]=$i}END{for(i=1;i<=NF;i++) {for(j=1;j<=NR;j++) printf a[i,j] " ";print ""}}'

# 行转列：x指多少行拼接成一列
cat /etc/passwd | xargs -nx
```

