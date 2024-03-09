## FastApi

### 1.quicklyStart

#### 1.1.1 测试1

```python
pip install fastapi
pip install uvicorn

vim main.py
#----------
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def index():
    return {"msg": "hello world"}
#----------

# 在当前目录下命执行以下:
uvicorn main:app --reload --host 127.0.0.1 --port 8080
# main:app：启动main这个模块下的app这个可执行对象
# reload：表示代码修改会自动加载重启服务
# host：表示绑定的ip地址
# port：表示监听的端口号
```

#### 1.1.2 测试2

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()


@app.get("/")
def index():
    return {"msg": "hello china"}


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 1.2 路径参数

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()


@app.get("/")
def index():
    return {"msg": "hello world"}


@app.get("/phone")
def phone():
    return {"msg": "phone"}


@app.get("/phone/{phone_id}")
def query_phone(phone_id: int):
    return {"msg": "phone" + f"id:{phone_id}"}


@app.get("/phone/{phone_id}/{phone_color}")
def query_phone(phone_id: int, phone_color: str):
    # phone_id: int,动态路由,自动进行类型转换,如果前端传入的参数不匹配,不会进入路径函数
    return {"msg": "phone" + f"id:{phone_id}:{phone_color}"}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)

```



#### 1.3 路径函数优先级

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()


@app.get("/book/{book_id}")
def book(book_id: int):
    return f"{id}"


"""
直接访问  127.0.0.1:8080/book/info 会匹配到 上面的路径函数,然后出现类型匹配错误
这就是先匹配到上面的路径 把 info 作为 {book_id}
要想解决这个问题,可以将当前路径函数 移到上面函数的前面
或者将当前路径函数请求方法变成post,put... 作为区别

"""


@app.get("/book/info")
def book():
    return "hello world"


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 1.4 查询参数

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

books = list()
for i in range(1, 100):
    books.append(f"book{i}")

"""
127.0.0.1:8080/book?page=2&size=10
这个url 参数必须与路径操作函数变量名相匹配

查询自带的 openAPI文档
127.0.0.1:8080/docs  
127.0.0.1:8080/redoc 
"""


@app.get("/book")
def query_book(page: int, size: int):
    return {"books": books[(page - 1) * size:page * size]}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 1.5 openAPI文档说明

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI(title="API文档说明")

"""
查询自带的 openAPI文档
127.0.0.1:8080/docs  
127.0.0.1:8080/redoc 
"""


@app.get("/book", tags=["book"], summary="图书接口")
def book():
    return None


@app.post("/book", tags=["book"])
def book():
    return None


@app.get("/user", tags=["user"])
def user():
    return None


@app.get("/dev", tags=["book", "user"])
def dev():
    return None


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 2. path & query

#### 2.1 路径转换器

```python
import uvicorn
from fastapi import FastAPI

app = FastAPI()


@app.get("/user/{user_id:int}")  # int ,str ,float
def index1(user_id):
    print(type(user_id), user_id)
    return {"result": f"{user_id}"}


# 127.0.0.1:8080/user/apps/nginx/a.txt
# file
@app.get("/users/{file_path:path}")
def index2(file_path):
    print(type(file_path), file_path)
    return {"result": f"{file_path}"}  # file_path = apps/nginx/a.txt


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 2.2 路径参数枚举值

```python
import uvicorn
from fastapi import FastAPI
from enum import Enum


class TagName(str, Enum):  # 多继承,继承str类的一些属性
    PYTHON = "python"
    LINUX = "linux"
    JAVA = "java"


app = FastAPI()


@app.get("/{tag}")
def index(tag: TagName):  # tag 只接受枚举类 定义的值
    return {tag.name: tag.value}  # tag.name = PYTHON , tag.value = python


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 2.3 路径参数使用Path校验

```python
import uvicorn
from fastapi import FastAPI, Path

app = FastAPI()


@app.get("/name/{name}")
def index1(name: str = Path(max_length=10, min_length=3)):
    # Path函数用来限制 路径参数, 规定字符串长度
    return {"result": name}


@app.get("/age/{age}")
def index2(age: int = Path(gt=3, lt=10)):
    # Path函数用来限制 路径参数, 规定参数取值范围
    return {"result": age}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True) 
```



#### 2.4 查询参数

```python
import typing

import uvicorn
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/")
def index(page: int = 1, size: int = 10):
    # 给参数新增默认值,避免前端未传参数而导致报错
    return {
        "result": "{},{}".format(page, size)
    }


@app.get("/para")
def index2(name: str, is_student: bool, age: int = 18):
    # 注意参数的位置,先是位置参数(没有设置默认值的参数),其次就是关键字参数(有默认值参数)
    pass


@app.get("/ids")  # 127.0.0.1:8080/ids?uid=1&uid=22
def index3(uid: typing.List[int] = Query()):
    return uid


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 2.5 查询参数使用Query校验

```python
import typing

import uvicorn
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/index")
def index(name: typing.Optional[str] = Query(default=..., max_length=5)):
    # typing.Optional[str] 规定请求参数的类型
    # Query(default=...,max_length=5) 规定参数的长度
    print(type(name))
    return {"result": name}


@app.get("/index/{name}")
def index2(name: str):
    """
    一个请求同时包含同名的路径参数和查询参数,以路径参数为准
    如果没有路径参数,路径函数的变量都从 查询参数中获取值
    127.0.0.1:8080/index/linux?name=hxh  -> 返回的结果是 linux
    127.0.0.1:8080/index/linux           -> 返回的结果是 linux
    """
    return {"result": name}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 3. request

#### 3.1 header接收请求头参数

```python
import typing

import uvicorn
from fastapi import FastAPI, Header

app = FastAPI()


@app.get("/index")
def index1(ua: str = Header(alias="User-Agent"),
           host: str = Header(default="Host")):
    # Header() 获取请求头中名为 User-Agent 的信息
    return {
        "ua": ua,
        "req_url": host
    }


@app.get("/token")
def index2(token: str = Header(alias="token")):
    return {"token": token}


@app.get("/token2")
def index2(token: typing.List[str] = Header(alias="x-token")):
    # typing.List[str] 同时获取多个值
    return {"token": token}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 3.2 使用请求体发送数据

```python
import uvicorn
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Pet(BaseModel):
    pet_name: str = "dog"
    pet_color: str


class User(BaseModel):
    username: str
    passwd: str
    pet: Pet


@app.post("/login")
def login(user: User):
    """
    接收前端传过来的请求体数据,需要导入 BaseModel,
    自定义类继承 BaseModel,类相关属性名跟前端传过来变量名一致,无顺序要求
    在路径操作函数中,规定传入的数据类型,为自定义的类,fastAPI会自动进行相应处理

    post 127.0.0.1:8080/logion
    {
        "username": "hxh",
        "passwd": "root",
        "pet":{
                "pet_name":"dog",
                "pet_color":"red"
        }
    }
    """
    return {"result": user}


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 3.3 使用body接收请求体数据

```python
import typing

import uvicorn
from fastapi import FastAPI, Body
from pydantic import BaseModel

app = FastAPI()


class User(BaseModel):
    username: str
    passwd: str


class Users(BaseModel):
    ulist: typing.List[User]
    hobby:typing.List[str]


@app.post("/login")
def login(user: User = Body(embed=True),
          age: int = Body(gt=18, lt=35)):
    """
    使用body 获取单个值
    Body(embed=True) 对象嵌套在{}中使用
    {
        user:{
            "username": "hxh",
            "passwd": "root"
        },
        age:18
    }

    """
    return {"result": user, "age": age}


@app.post("/login2")
def login2(users: Users):
    """
    {
        "ulist":[
            {"username":"hxh1","passwd":"123"},
            {"username":"hxh2","passwd":"1"}
        ],
        "hobby":["1","2","3"]
    }
    """
    print(users)
    return {
        "user": users,
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 3.4 请求体使用Filed校验

```python
import dataclasses
import typing

import uvicorn
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class Item(BaseModel):
    """
    max_length 规定字符串最大长度
    gt 规定值必须大于18
    min_items 规定最小元素个数是2
    """
    name: str = Field(max_length=5)
    age: int = Field(gt=18)
    hobby: typing.Set[str] = Field(min_items=2, max_items=5)

    # 给请求体提供示例 127.0.0.1:8080/docs 查看示例
    class Config(object):
        json_schema_extra = {
            "example": {
                "name": "hxh",
                "age": 18,
                "hobby": ["music", "coding", "game"]
            }
        }


@dataclasses.dataclass  # 继承BaseModel,和 @dataclasses.dataclass 效果是一样的
class Item2(object):
    # example 给字段一个示例值
    name: str = Field(max_length=5, example="xxx")
    age: int = Field(gt=18, example=19)
    hobby: typing.Set[str] = Field(min_items=2, max_items=5, example=["a", "b"])


@app.get("/1")
def index(item: Item):
    print(item)
    pass


@app.get("/2")
def index(item: Item2):
    print(item)
    pass


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 4. response

#### 4.1 设置响应头

```python
import uvicorn
from fastapi import FastAPI, status, Request, Response

app = FastAPI()


@app.get("/login", status_code=status.HTTP_200_OK)
def login(req: Request, resp: Response):
    # status_code 设置响应状态码
    # 获取相请求头信息
    req_token = req.headers.get("x-token")
    print(req.url)
    # 设置响应头
    resp.headers["jwt_token"] = "resp_jwt_token"
    resp.headers.append("cookies", "resp_cookies")
    return {
        "req_token": req_token
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 4.2 设置响应体数据

```python
import typing

import uvicorn
from fastapi import FastAPI, status
from pydantic import BaseModel
from fastapi.responses import JSONResponse

app = FastAPI()


class User(BaseModel):
    username: str
    passwd: str


class UserOut(BaseModel):
    username: str


@app.post("/login")
def login(user: User):
    resp = JSONResponse(
        content=user.model_dump(),
        status_code=status.HTTP_200_OK,
        headers={"jwt_token": "resp_jwt_tokens"},
    )
    return resp


@app.post("/register", response_model=UserOut)
def register(user: User):
    """
    response_model=UserOut 会返回 与响应模型参数相对应的字典
    因为设置了response_model 所以不会返回 passwd字段
    return user -> return {"username": xxx}
    """
    return user  # 等价于 return user.dict()


@app.get("/test1", response_model=typing.Dict[str, str])
def test():
    # response_model=typing.Dict[str, str]
    # 返回数据时,会自动进行参数校验
    return {"xx": "11"}


class Item(BaseModel):
    name: str
    # typing.Union[str, None] 参数类型是str或者None
    description: typing.Union[str, None] = None
    price: float
    tax: float = 10.5


@app.post("/test2",
          response_model=Item,  # 设置相应模型
          response_model_include={"name", "price"},  # 响应字段
          response_model_exclude={"tax"},  # 排除一些字段
          response_model_exclude_none=True,  # 排除空值字段
          response_model_exclude_unset=True  # 排除未设置值字段
          )
def test2(item: Item):
    return item


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 4.3 jsonable_encoder

```python
from datetime import datetime
import json

import uvicorn
import typing
from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: typing.Union[str, None] = None
    price: float
    tax: float = 10.5


@app.get("/1")
def index1(item: Item):
    # 用法类似 response_model_include
    # return item 等价于  return item.dict()
    return item.model_dump(include={"name", "price"}, exclude_defaults=True)


class Item2(object):
    def __init__(self, username, time):
        self.username = username
        self.time = time

    def __str__(self):
        return f"username:{self.username},time:{self.time}"


@app.get("/2")
def index2():
    item2 = Item2("hxh", datetime.now())
    # include 只序列化指定字段,其他字段直接丢弃
    # 一般直接使用 jsonable_encoder(item2)
    encoder = jsonable_encoder(item2, include={"time"})
    print(encoder, type(encoder))
    return {"result": json.dumps(encoder)}
    # return json.dumps(item2)  # is not JSON serializable


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 5.Demo

#### 5.1 blogCRUD

```python
import uvicorn
from fastapi import FastAPI, HTTPException, status, Query
from pydantic import BaseModel, Field

app = FastAPI(title="blog CRUD")

blogs = [{"blog_id": i, f"blog_content{i}": f"content_desc{i}"}
         for i in range(1, 100)]


@app.get("/blog", tags=["blog"])
def get(page: int = 1, page_size: int = 10):
    return {
        "result": blogs[(page - 1) * page_size:page * page_size]
    }


@app.get("/blog/{blog_id}", tags=["blog"])
def query_blog_by_id(blog_id: int):
    for item in blogs:
        if item.get("blog_id") == blog_id:
            return {
                "result": item
            }

    # 未达到预期结果,抛出异常信息 from fastapi import HTTPException
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail=f"{blog_id} is not found!"
    )


class Blog(BaseModel):
    blog_id: int
    blog_content: str = Field(min_length=5, max_length=500)


"""
{
    "blog_content":"xxxxxxx"
}
"""


@app.post("/blog", tags=["blog"])
def add_blog(blog: Blog):
    blog_id = len(blogs) + 1
    blogs.append({"blog_id": blog_id, f"blog_content": blog.blog_content})
    return {
        "result": blogs[len(blogs) - 1]
    }


"""
{
    "blog_id":11,
    "blog_content":"xxxxxxx"
}
"""


@app.put("/blog", tags=["blog"])
def update_blog(blog: Blog):
    for item in blogs:
        if blog.blog_id == item.get("blog_id"):
            blogs[blog.blog_id - 1] = {"blog_id": blog.blog_id,
                                       "blog_content": blog.blog_content}
            return {
                "result": blogs[blog.blog_id - 1]
            }

    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail=f"{blog.blog_id} is not found!"
    )


@app.delete("/blog/{blog_id}", tags=["blog"])
def del_blog(blog_id: int):
    for item in blogs:
        if item.get("blog_id") == blog_id:
            # blogs.remove(item) # 一般只进行逻辑删除
            return {
                "result": item
            }
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail=f"{blog_id} is not found!"
    )


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 6. form

#### 6.1 formDemo

```python
import uvicorn
from fastapi import FastAPI, Form, File

app = FastAPI()


@app.post("/login")
def login(username: str = Form(), password: str = Form()):
    # username: str = Form() 标识username数据值从表单获取,而不是查询参数
    # 使用Form需要安装包: pip install python-multipart
    return {
        "username": username,
        "password": password
    }


@app.post("/upload")
def upload(files: bytes = File()):
    # 这种方式将文件读取到内存中,只适合小文件上传
    with open("./xx.txt", "wb") as f:
        f.write(files)
    return None


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 6.2 UploadFile

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!-- 默认值:enctype="application/x-www-form-urlencoded" -->
<form action="http://localhost:8080/login" method="post" enctype="application/x-www-form-urlencoded">
    <label>
        用户名:
        <input type="text" name="username">
    </label><br>
    <label>
        密码:
        <input type="password" name="password">
    </label><br>
    <input type="submit" value="login">
</form>

<!-- enctype="multipart/form-data" 用于上传文件或者普通数据 -->
单文件上传
<form action="http://localhost:8080/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="upload">
</form>

<hr>
多文件上传
<form action="http://localhost:8080/uploads" method="post" enctype="multipart/form-data">
    <input type="file" name="files" multiple>
    <input type="submit" value="uploads">
</form>
</body>
</html>
```

```python
import typing

import uvicorn
from fastapi import FastAPI, UploadFile, File

app = FastAPI()


@app.post("/upload")
async def upload(file: UploadFile):
    # 后端定义file变量 与前端 form 定义的name值一致 <input type="file" name="file">
    content = file.file.read()  # 获取文件内容
    await file.close()
    print(file.filename)  # 不直接读取文件内容,只获取文件一些属性
    print(f"{int(file.size / 1024 / 1024)} M")
    print(file.content_type)
    return None


@app.post("/test")
def upload2(file: typing.Optional[UploadFile] = None):
    # 设置文件非必传
    if file.size != 0:
        return {"result": file.filename}
    return {"result": "no selected file"}


@app.post("/uploads")
def upload3(files: typing.List[UploadFile] = None):
    # 上传多个文件
    if files is not None:
        for item in files:
            print(item.filename)


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 6.3 综合示例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="http://localhost:8080/register" method="post" enctype="multipart/form-data">
    <input type="text" name="username">
    <input type="password" name="password">
    <input type="file" name="head_pic">
    <input type="file" name="hobby_img" multiple>
    <input type="submit" value="register" multiple>
</form>
</body>
</html>
```



```python
import typing

import uvicorn
from fastapi import FastAPI, UploadFile, File, Form

app = FastAPI()


@app.post("/register")
def register(
        # 当Form和File同时使用,需要将File放到Form前面
        head_pic: bytes = File(default=None),
        username: str = Form(),
        password: str = Form(),
        hobby_img: typing.List[UploadFile] = None
):
    return {
        "username": username,
        "password": password,
        "head_pic": len(head_pic),
        "hobby_img": len(hobby_img)
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 7. DI

#### 7.1 依赖注入

```python
import uvicorn
from fastapi import FastAPI, Depends

app = FastAPI()


@app.get("/1")
def index1(page: int, size: int, status: bool):
    return {
        "result": f"{(page - 1) * page}:{page * size}:{status}"
    }


def common_para(page: int = 1, size: int = 10, status: bool = True):
    return {
        "page": page,
        "size": size,
        "status": status
    }


@app.get("/common")
def common(commons: dict = Depends(common_para)):
    # common_para 函数的返回值作为 common参数的默认值
    page = commons["page"]
    size = commons["size"]
    status = commons["status"]
    return {
        "result": f"{(page - 1) * page}:{page * size}:{status}"
    }


@app.get("/2")
def index2(commons: dict = Depends(common_para)):
    page = commons["page"]
    size = commons["size"]
    status = commons["status"]
    return {
        "result": f"{(page - 1) * page}:{page * size}:{status}"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 7.2 嵌套依赖

```python
import typing

import uvicorn
from fastapi import FastAPI, Depends

app = FastAPI()


def get_username(username: typing.Optional[str] = None):
    return username


def get_nickname(nickname: typing.Optional[str] = "hxh"):
    return nickname


def username_or_nickname(
        username: typing.Optional[str] = Depends(get_username),
        nickname: typing.Optional[str] = Depends(get_nickname)):
    if username:
        return username
    return nickname


@app.get("/index")
def index(name: str = Depends(username_or_nickname)):
    return {"result": name}


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 7.3 依赖注入缓存现象

```python
import typing

import uvicorn
from fastapi import FastAPI, Depends

app = FastAPI()


def get_num():
    print("执行 get_num ...")
    return 111


@app.get("/1")
def test1(num: int = Depends(get_num), size: int = Depends(get_num)):
    # 两个查询参数默认值分别依赖同一个 函数返回值,只会执行依赖函数一次,默认使用缓存
    return {"result": num}


@app.get("/2")
def test1(num: int = Depends(get_num), size: int = Depends(get_num, use_cache=False)):
    # use_cache = False 不适用依赖 缓存的值,执行 访问这个路径函数则依赖函数两次
    return {"result": num}


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 7.4 全局依赖

```python
import uvicorn
from fastapi import FastAPI, Depends, Header, HTTPException

app = FastAPI()


def verify_token(x_token: str = Header(default=None)):
    if x_token is None or "linux" != x_token:
        raise HTTPException(
            status_code=403,
            detail="no permissions"
        )


@app.get("/login", dependencies=[Depends(verify_token)])
def login():
    # dependencies=[Depends(verify_token)] 路径装饰器使用依赖注入,设置访问权限校验
    return {
        "result": "hello"
    }


@app.get("/order", dependencies=[Depends(verify_token)])
def order():
    # dependencies=[Depends(verify_token)] 路径装饰器使用依赖注入,设置访问权限校验
    return {
        "result": "order"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```

```python
import uvicorn
from fastapi import FastAPI, Depends, Header, HTTPException, Request


def verify_token(req: Request, x_token: str = Header(default=None)):
    if req.url.path in ["/", "/test"]:
        # 访问一些特定的页面不需要检验
        return

    if x_token is None or "linux" != x_token:
        raise HTTPException(
            status_code=403,
            detail="no permissions"
        )


app = FastAPI(dependencies=[Depends(verify_token)]) # 全局路径函数校验


@app.get("/")
def index():
    return {"result": "index"}


@app.get("/test")
def index():
    return {"result": "test"}


@app.get("/login")
def login():
    return {
        "result": "hello"
    }


@app.get("/order", dependencies=[Depends(verify_token)])
def order():
    return {
        "result": "order"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 7.5 基于类,对象的依赖注入

```python
import typing

import uvicorn
from fastapi import FastAPI, Depends

app = FastAPI()


class Common_class:
    def __init__(self, name: str = "linux", age: int = 18, query=None):
        self.name = name
        self.age = age
        self.query = query

    def __call__(self) -> typing.Any:
        print("cccc")
        return self.query


@app.get("/1")
def index(common: Common_class = Depends(Common_class)):
    # 基于类的依赖注入
    return {
        "result": common
    }


@app.get("/2")
def index(common: typing.Any = Depends(Common_class(query="hello"), use_cache=False)):
    print(common)
    # 基于对象注入
    return {
        "result": common
    }


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)
```



#### 7.6 依赖注入使用yield

```python
import uvicorn
from fastapi import FastAPI, Depends

app = FastAPI()


class Mysql(object):
    def query(self):
        pass

    def close(self):
        pass


def get_db():
    db = Mysql()
    try:
        yield db
    finally:
        db.close()


@app.get("/query")
def query_book(db: Mysql = Depends(get_db)):
    return {"result": db.query()}


if __name__ == '__main__':
    uvicorn.run("main:app", host="127.0.0.1", port=8080, reload=True)

```



### 8. ORM

#### 8.1 pymysql连接

```python
#  pip install pymysql
import pymysql
from pymysql import Connection


def get_db():
    # 连接数据库
    return Connection(
        host="192.168.10.202",
        port=3306,
        user="root",
        password="root",
        database="fastapi_test",
        cursorclass=pymysql.cursors.DictCursor  # 结果集封装到
    )


def execute_sql(db: Connection):
    # 执行sql语句
    cursor = db.cursor()  # 获取游标对象
    total_count = cursor.execute("select * from user")
    print(total_count)  # 获取的是 多少条结果

    # for i in range(0, total_count):
    #     print(cursor.fetchone())

    print(cursor.fetchmany(10))  # 取10条数据
    print(cursor.fetchall())  # 取出剩下所有数据
    cursor.close()


def main():
    db = get_db()
    execute_sql(db)
    db.close()


if __name__ == '__main__':
    main()

```



#### 8.2 pymsqlCRUD

```python
import pymysql


def get_db():
    # 连接数据库
    db = pymysql.Connection(
        host="192.168.10.202",
        port=3306,
        user="root",
        password="root",
        database="fastapi_test",
        autocommit=True,  # 自动提交事务,默认False
        cursorclass=pymysql.cursors.DictCursor  # 结果集封装到
    )

    cursor = db.cursor()
    return db, cursor


def query():
    db, cursor = get_db()
    user_id = 202
    try:
        cursor.execute("select * from user where id = %s", user_id)
        print(cursor.fetchone())

        cursor.execute("insert into user(username,password) values('test','123')")
        # 批量新增
        insert_sql = "insert into user(username,password) values(%s,%s)"
        args_insert = [("test11", 1111), ("test22", 2222)]
        cursor.executemany(insert_sql, args_insert)

        cursor.execute("update user set username = 'test2' where id = %s", user_id)
        # 批量更新
        update_sql = "update user set username = 'test33' where id = %s"
        args_update = [i for i in range(220, 230)]
        cursor.executemany(update_sql,args_update)

        cursor.execute("delete from user where id = %s", user_id)
        # 批量删除
        del_sql = "delete from user where id = %s"
        args_del = [i for i in range(210, 220)]
        cursor.executemany(del_sql, args_del)

        # 默认开启了事务,保存修改需要提交事务
        # db.commit() , 已配置自动提交事务 autocommit=True

    finally:
        cursor.close()
        db.close()


def main():
    query()


if __name__ == '__main__':
    main()
```



#### 8.3s SqlAlchemy

```python
# pip install sqlalchemy
import pymysql
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
from sqlalchemy import Column, String, Integer
from sqlalchemy.orm import declarative_base

# 将pymysql设置成底层驱动
pymysql.install_as_MySQLdb()

# 创建模型类对象
BaseModel = declarative_base()


def get_db():
    DATABASE_URL = "mysql://root:root@192.168.10.202:3306/fastapi_test"
    # 基于引擎[create_engine(DATABASE_URL)]创建session对象,
    # 通过session对象调用__call__方法[sessionmaker(...)]得到 db对象
    db: Session = sessionmaker(bind=create_engine(DATABASE_URL))()
    return db


class User(BaseModel):
    __tablename__ = "user"
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(10))
    password = Column(String(10))

    def __str__(self):
        return f"{self.id}:{self.username}:{self.password}"


def query():
    db = get_db()

    u1 = db.query(User).filter(User.id == 250).first()
    print(u1)
    list_user = db.query(User).filter(User.id > 300).all()
    list_user = db.query(User).filter(User.id > 300).offset(0).limit(5).all()
    for item in list_user:
        print(item)

    db.close()


def insert():
    db = get_db()
    # 新增一条数据
    db.add(User(username="xxxx", password="dsadsad"))
    # 新增多条数据
    db.add_all([User(username="hxh1", password="xx"), User(username="hxh2", password="xxx")])
    db.bulk_save_objects([User(username="hxh3", password="xx"), User(username="hxh5", password="xxx")])

    db.commit()
    db.close()


def update():
    db = get_db()
    # 先查询 再修改
    u1: User = db.query(User).filter(User.id == 203).first()
    u1.username = "china"

    # 直接修改
    db.query(User).filter(User.id == 204).update({"username": "hxh", "password": "china"})

    db.commit()
    db.close()


def delete():
    db = get_db()
    db.query(User).filter(User.id == 205).delete()
    db.commit()
    db.close()


def main():
    query()
    insert()
    update()
    delete()


if __name__ == '__main__':
    main()
```



#### 8.4 fastapi集成sqlalchemy

```python
# pip install sqlalchemy
import typing

import pymysql
import uvicorn
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
from sqlalchemy import Column, String, Integer
from sqlalchemy.orm import declarative_base
from fastapi import FastAPI, Depends
from pydantic import BaseModel as SchemyModel

# 将pymysql设置成底层驱动
pymysql.install_as_MySQLdb()

# 创建模型类对象
BaseModel = declarative_base()


class User(BaseModel):
    __tablename__ = "user"  # 数据库表名
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(10))
    password = Column(String(10))

    def __str__(self):
        return f"{self.id}:{self.username}:{self.password}"


def get_db():
    DATABASE_URL = "mysql://root:root@192.168.10.202:3306/fastapi_test"
    # 基于引擎[create_engine(DATABASE_URL)]创建session对象,
    # 通过session对象调用__call__方法[sessionmaker(...)]得到 db对象
    db: Session = sessionmaker(bind=create_engine(DATABASE_URL))()

    try:
        yield db
    finally:
        db.close()


app = FastAPI()


@app.get("/user/{user_id}")
def query_by_id(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    if user:
        return {
            "user": user
        }
    return {"user": None}


@app.delete("/user/{user_id}")
def del_user(user_id: int, db: Session = Depends(get_db)):
    try:
        db.query(User).filter(User.id == user_id).delete()
        db.commit()
        return {
            "result": "delete success"
        }
    except:
        return {
            "result": "delete fail"
        }


class UserBase(SchemyModel):
    username: str


class UserIn(UserBase):
    id: typing.Optional[int] = None
    password: str


class UserOut(UserBase):
    id: int

    class Config:
        orm_model = True


@app.put("/user")
def add_user(user: UserIn, db: Session = Depends(get_db)):
    try:
        # db.add(User(username=user.username, password=user.password))

        obj = User(**user.model_dump())
        db.add(obj)
        db.commit()
        db.refresh(obj)
        return {
            "result": "add user success"
        }
    except Exception as e:
        print(e)
        return {
            "result": "add user fail"
        }


@app.patch("/user")
def update_user(user: UserIn, db: Session = Depends(get_db)):
    if user.id is not None:
        try:
            u1: User = db.query(User).filter(User.id == user.id).first()
            u1.username = user.username
            u1.password = user.password
            db.commit()
            return {
                "result": "update user success"
            }
        except:
            return {
                "result": "update user fail"
            }
    return {
        "result": "id is None"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 9. auth

#### 9.1 用户注册,登录

```python
import hashlib

import pymysql
import uvicorn
from fastapi import FastAPI, Form, Depends
from sqlalchemy import Column, String, Integer
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base
from sqlalchemy.orm import sessionmaker, Session

# 将pymysql设置成底层驱动
pymysql.install_as_MySQLdb()

# 创建模型类对象
BaseModel = declarative_base()

app = FastAPI()


def get_db():
    DATABASE_URL = "mysql://root:root@192.168.10.202:3306/fastapi_test"
    # 基于引擎[create_engine(DATABASE_URL)]创建session对象,
    # 通过session对象调用__call__方法[sessionmaker(...)]得到 db对象
    db: Session = sessionmaker(bind=create_engine(DATABASE_URL))()
    try:
        yield db
    finally:
        db.close()


class User(BaseModel):
    __tablename__ = "user"
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(20))
    password = Column(String(50))

    def __str__(self):
        return f"{self.id}:{self.username}:{self.password}"


def md5_str(text: str, salt: str = "add salt") -> str:
    """
    :param salt: 盐
    :param text: 需要加密的文本
    :return: 加密后的文本
    """
    m = hashlib.md5(salt.encode("utf-8"))
    m.update(text.encode("utf-8"))
    return m.hexdigest()


@app.post("/register")
def register(
        username: str = Form(max_length=10),
        password: str = Form(max_length=10),
        db: Session = Depends(get_db)
):
    password = md5_str(password)
    if db.query(User).filter(User.username == username).first():
        return {"result": "用户已存在"}

    user = User(username=username, password=password)
    db.add(user)
    db.commit()
    db.refresh(user)
    return {
        "result": f"{username} 注册成功"
    }


@app.post("/login")
def login(
        username: str = Form(max_length=10),
        password: str = Form(max_length=10),
        db: Session = Depends(get_db)
):
    user: User = db.query(User).filter(User.username == username).first()
    if md5_str(password).__eq__(user.password):
        return {
            "result": user.username
        }
    return {
        "result": "用户名或密码错误"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 9.2 请求头验证

```python
import typing

import uvicorn
from fastapi import FastAPI, Header, HTTPException, Form, Response

app = FastAPI()

token = "hxh"


@app.post("/login")
def login(
        resp: Response = None,
        username: str = Form(default=None),
        password: str = Form(default=None)
):
    resp.headers["x_token"] = token
    resp.status_code = 201
    # ... 假设登录成功,返回token
    return {
        "result": "login success"
    }


@app.get("/order")
def access_order(x_token: typing.Optional[str] = Header()):
    if token.__eq__(x_token):
        return {
            "result": "success"
        }
    raise HTTPException(
        status_code=403,
        detail="未通过 token 校验"
    )


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)

```



#### 9.2 cookie

```python
import typing

import uvicorn
from fastapi import FastAPI, HTTPException, Response, Cookie

app = FastAPI()


@app.get("/login")
def login(
        username: str,
        resp: Response = None
):
    resp.set_cookie("auth_token", value=username, max_age=60 * 60)
    # ... 假设登录成功,返回token
    return {
        "result": "login success"
    }


@app.get("/order")
def access_order(auth_token: typing.Optional[str] = Cookie(default=None)):
    if auth_token:
        return {
            "result": "success"
        }
    raise HTTPException(
        status_code=403,
        detail="未通过 cookie 校验"
    )


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 9.4 jwt

```python
# pip install pyjwt
import typing
import jwt
import uvicorn
from fastapi import FastAPI, HTTPException, Response, Header
import datetime

app = FastAPI()

jwt_salt = "hxh"


@app.get("/login")
def login(
        username: str,
        resp: Response = None
):
    # ... 假设登录成功,返回token

    # 设置jwt过期时间,exp的键名是固定写法
    exp = datetime.datetime.now() + datetime.timedelta(1) # 1天后过期
    token = jwt.encode(payload={"username": username, "exp": exp}, key=jwt_salt)
    resp.headers["token"] = token
    return {
        "result": "login success"
    }


@app.get("/order")
def access_order(token: typing.Optional[str] = Header()):
    # 返回jwt的载荷
    try:
        payload = jwt.decode(token, key=jwt_salt, algorithms="HS256")
        return {
            "result": payload["username"]
        }
    except Exception:
        raise HTTPException(
            status_code=403,
            detail="未通过 cookie 校验"
        )


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 9.5 api文档认证工具

```python
#  pip install "python-jose[cryptography]"
import uvicorn
import datetime
from fastapi import FastAPI, Form, HTTPException, Depends
from fastapi.security import OAuth2PasswordBearer
from jose import jwt, JWTError
from jose.constants import ALGORITHMS

# 访问http://127.0.0.1:8080/docs 添加认证信息
oauth = OAuth2PasswordBearer(tokenUrl="/login")

app = FastAPI()

jwt_salt = "hxh"
exp = datetime.datetime.now() + datetime.timedelta(1)


@app.post("/login")
def login(
        username: str = Form(),
        password: str = Form()
):
    # ... 省去数据库查询步骤
    payload = {"username": username, "exp": exp}
    access_token = jwt.encode(claims=payload, key=jwt_salt, algorithm=ALGORITHMS.HS512)
    return {
        "access_token": access_token,
        "token_type": "bearer"
    }


@app.get("/order")
def access_order(token: str = Depends(oauth)):
    try:
        result = jwt.decode(token, key=jwt_salt, algorithms=ALGORITHMS.HS512)
        print(result)
        return {
            " ": result["username"]
        }
    except JWTError:
        raise HTTPException(
            status_code=403,
            detail="error token"
        )


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 9.6 ApiRouter

```python
from fastapi import APIRouter

# router 用法跟 FastApi() 一致
router = APIRouter()


@router.get("/user/login")
def login():
    pass


@router.get("/user/register")
def register():
    pass
```

```python
from fastapi import APIRouter

# router 用法跟 FastApi() 一致
router = APIRouter()


@router.post("/pay/wechat")
def pay_with_wechat():
    pass


@router.post("/pay/alipay")
def pay_with_alipay():
    pass

```

```python
import uvicorn

from router import userApi, payApi
from fastapi import FastAPI

app = FastAPI()

app.include_router(userApi.router, tags=["user"])
app.include_router(payApi.router, tags=["pay"])

if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 10. 请求,响应对象

#### 10.1 Request,Response

```python
from fastapi import FastAPI, Request, Response
import json

app = FastAPI()


@app.get("/{item_id}")
def hello(req: Request):
    item_id = req.path_params.get("item_id")
    page = req.query_params.get("page", 0)
    size = req.query_params.get("size", 10)
    x_token = req.headers.get("x-token")
    x_token_cookie = req.cookies.get("x_token")

    return {
        "item_id": item_id,
        "page": page,
        "size": size,
        "x-token": x_token,
        "cookie": x_token_cookie,
        "client_host": req.client.host
    }


@app.get("/", )
def hello():
    response = Response(
        content=json.dumps({"hello": "world"}),
        status_code=201,
        headers={"x-token": "qqqqqq"},
        media_type="application/json"
    )

    return response
  
if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 10.2 响应的类型

```python
import uvicorn
from fastapi import FastAPI, Response
from fastapi.responses import (JSONResponse, PlainTextResponse,
                               HTMLResponse, StreamingResponse,
                               FileResponse, RedirectResponse)

# 默认全局响应类型是 response_class= JSONResponse
app = FastAPI(default_response_class=JSONResponse)


@app.get("/text", response_class=PlainTextResponse)
def return_text():
    # 修改默认响应类型 response_class = PlainTextResponse
    # 响应纯文本数据
    return Response(
        media_type="text/plain",
        content="hello"
    )
    # return "hello"


@app.get("/html", response_class=HTMLResponse)
def return_text():
    content = """
            <!DOCTYPE html>
            <html lang="en">
                <head>
                    <meta charset="UTF-8">
                    <title>Title</title>
                </head>
                <body>
                    <h1>hello world</h1>
                </body>
            </html>
        """
    # return HTMLResponse(content=content)
    return Response(media_type="text/html", content=content)


@app.get("/stream", response_class=StreamingResponse)
def return_stream():
    def download_file():
        with open("./a.zip", "rb") as f:
            yield from f

    # 流式下载,一点点读取,一点点下载
    return StreamingResponse(download_file(), media_type="application/zip")


@app.get("/file", response_class=FileResponse)
def return_file():
    # path 文件所在路径
    # filename 下载的文件名
    return FileResponse(path="./a.txt", filename="z.txt")


@app.get("/redirect", response_class=RedirectResponse)
def return_redirect():
    # 重定向到其他页面
    return RedirectResponse(url="http://mshare.top")


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



### 11.async

#### 11.1 aiohttp

```python
import aiohttp
from fastapi import FastAPI


app = FastAPI(title="使用aiohttp")


async def baidu_html():
    async with aiohttp.ClientSession() as session:
        async with session.get('http://www.baidu.com') as response:
            return {
                "status": response.status,
                "content-type": response.headers['content-type'],
                "body": await response.text()
            }


@app.get("/")
async def index():
    return await baidu_html()		# 需要使用await 才能执行baidu_html 这个协程函数
```



#### 11.2 aiomysql

```python
import aiomysql
from aiomysql.cursors import DictCursor

from fastapi import FastAPI

app = FastAPI(title="使用aiomysql")


async def aiomysql_demo():
    # 获取连接对象
    conn = await aiomysql.connect(
        host="127.0.0.1",
        port=3306,
        user="root",
        password="12345",
        db="db",
        cursorclass=DictCursor  # 返回字典格式的数据
    )
    # 创建游标
    cur = await conn.cursor()
    # 执行SQL
    await cur.execute("SELECT * from users;")
    # 获取SQL结果
    result = await cur.fetchall()
    # 关闭CURSOR
    await cur.close()
    # 关闭连接
    conn.close()

    return result


@app.get("/")
async def index():
    return await aiomysql_demo()

```



#### 11.3 databases

```python
from fastapi import FastAPI
from databases import Database

app = FastAPI(title="使用databases")

"""
在异步世界中操作数据库，比如MySQL，我们需要只用 aiomysql，且需要自己手写SQL语句。

使用其他类型的数据库，比如PostgreSQL，则需要基于asyncpg或aiopg。

那样的就有一个问题，当我们的应用需要换一个数据库时，就需要调整基础代码，使用不灵活。

此时出现了一个工具，它封装了不同类型的数据库，我们只需要在使用它提供的接口操作数据库就行
而不用关心底层的数据库驱动，它就是encode出品的 databases。
"""


async def databases_demo():
    # 实例化一个db连接并建立连接
    database = Database('mysql://root:12345@localhost:3306/db')
    await database.connect()

    # Run a database query.
    query = "SELECT * FROM users"
    rows = await database.fetch_all(query=query)
    return rows


@app.get("/")
async def index():
    return await databases_demo()

```



#### 11.4 tortoise-orm

```python
from fastapi import FastAPI
from tortoise import fields
from tortoise.models import Model
from tortoise.contrib.fastapi import register_tortoise

app = FastAPI(title="使用tortoise orm")

# pip3 install tortoise-orm
# 定义模型表
class User(Model):
    id = fields.IntField(pk=True)
    username = fields.CharField(max_length=255)
    password = fields.CharField(max_length=255)
    email = fields.CharField(max_length=255)

    class Meta:
        table = "users"  # 表示这个表对应数据库中的表名


# 使用register_tortoise 注册数据库信息
register_tortoise(
    app,
    db_url="mysql://root:12345@127.0.0.1:3306/db",
    modules={"models": ["main"]},  # 指定模型表所在的文本，"main" 表示mian.py中定义了User模型表
)


@app.get("/")
async def index():
    user = await User.filter(username="liuxu").first()
    user.email = "1111"
    await user.save()

    # 常用的CRUD方法
    # fake_user = await User.create(username="111", password="111", email="111")
    # await User.filter(id=fake_user.id).update(username="Updated name")
    # await User.filter(id=1).delete()

    return user
```



### 12. websocket

#### 12.1 示例

> WebSocket简介：
> WebSocket是一种在单个TCP连接上进行全双工通信的协议。
> WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。
> 在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。
>
> WebSocket和HTTP的相同点和不同点：
> WebSocket和HTTP都是解决 客户端-服务端 通信的网络协议。
> HTTP是单向的，客户端先发起请求，服务端收到请求后再处理这个请求返回响应结果；即先有请求再有响应，且是一个请求对应一个响应；请求时建立连接，响应后断开连接。
> WebSocket是双向的，客户端发起请求经过三次握手连接后，客户端和服务端就会基于这个连接保持通信，都可以主动向对方发消息。任何一端都可以主动断开这个连接。
> HTTP是无状态的，WebSocket是有状态的。即如果使用WebSocket，那么只要建立了连接，每次通信时，服务端都知道在和谁通信，不需要借助Cookie或seesion或jwt等登录认证方式。

```python
from typing import List

from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from fastapi.responses import HTMLResponse

app = FastAPI()

html = """
<!DOCTYPE html>
<html>
    <head>
        <title>Chat</title>
    </head>
    <body>
        <h1>WebSocket Chat</h1>
        <h2>Your ID: <span id="ws-id"></span></h2>
        <form action="" onsubmit="sendMessage(event)">
            <input type="text" id="messageText" autocomplete="off"/>
            <button>Send</button>
        </form>
        <ul id='messages'>
        </ul>
        <script>
            var client_id = Date.now()
            document.querySelector("#ws-id").textContent = client_id;
            var ws = new WebSocket(`ws://localhost:8000/ws/${client_id}`);
            ws.onmessage = function(event) {
                var messages = document.getElementById('messages')
                var message = document.createElement('li')
                var content = document.createTextNode(event.data)
                message.appendChild(content)
                messages.appendChild(message)
            };
            function sendMessage(event) {
                var input = document.getElementById("messageText")
                ws.send(input.value)
                input.value = ''
                event.preventDefault()
            }
        </script>
    </body>
</html>
"""


class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []

    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)

    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)

    async def send_personal_message(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)

    async def broadcast(self, message: str):
        for connection in self.active_connections:
            await connection.send_text(message)


manager = ConnectionManager()


@app.get("/")
async def get():
    return HTMLResponse(html)


@app.websocket("/ws/{client_id}")
async def websocket_endpoint(websocket: WebSocket, client_id: int):
    await manager.connect(websocket)	# 接收连接
    try:
        while True:
            data = await websocket.receive_text()		# 接收数据
            await manager.send_personal_message(f"You wrote: {data}", websocket)	# 自己发的消息自己看到
            await manager.broadcast(f"Client #{client_id} says: {data}")			# 广播，发给所有在线的客户端
    except WebSocketDisconnect:
        manager.disconnect(websocket)	# 断开连接
        await manager.broadcast(f"Client #{client_id} left the chat")	# 广播
```



### 13 其他

#### 13.1 部署

```bash
# 生成项目依赖包的文件列表
# pip freeze > ./requirements.txt
```



#### 13.2 事件钩子

```python
import uvicorn
from fastapi import FastAPI

app = FastAPI()


@app.on_event("startup")
def start():
    print("服务接收请求之前执行这个函数")


@app.on_event("shutdown")
def shutdown():
    print("服务结束之前执行这个函数")


@app.get("/")
def index():
    return {
        "result": "hello world"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 13.3 中间件

```python
import uvicorn
from fastapi import FastAPI, Request, Response

app = FastAPI()


@app.middleware("http")
async def middleware_fun1(req: Request, call_next):
    # 接收请求之前进入这个中间件函数,处理一些事情
    print(req.headers.get("user-agent"))
    return await call_next(req)


@app.middleware("http")
async def middleware_fun2(req: Request, call_next):
    # 接收请求之前进入这个中间件函数,处理一些事情
    print(req.headers.get("Host"))
    return await call_next(req)


@app.get("/")
def index():
    return {
        "result": "hello world"
    }


@app.get("login")
def login():
    return {
        "result": "login"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```



#### 13.4 后台任务

```python
import time

import uvicorn
from fastapi import FastAPI, BackgroundTasks

app = FastAPI()


def sendmail(email: str):
    print("into email func")
    # 模拟邮件发送
    time.sleep(5)
    return {
        "result": f"{email} send success"
    }


@app.get("/login")
def index(backgroundtasks: BackgroundTasks):
    # 添加后台任务,异步处理
    backgroundtasks.add_task(sendmail, "linux")
    backgroundtasks.add_task(sendmail, "hxh")
    return {
        "result": "success"
    }


if __name__ == '__main__':
    uvicorn.run("main:app", port=8080, reload=True)
```

