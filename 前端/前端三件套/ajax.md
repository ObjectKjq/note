get请求

```js
//创建对象
const xhr = new XMLHttpRequest();
//初始化，设置请求方法和url,如果是get请求可以再后面直接加参数
xhr.open('GET', 'http://127.0.0.1:8000/server?name=123');
//发送
xhr.send();
//事件绑定处理返回结果
//on when	当...时候
//readystate是xhr对象中的属性，表示0，1，2，3，4
//change改变
xhr.onreadystatechang = function(){
    //判断
    if(xhr.readyState === 4){
        //判断状态码200 404 403 401 500
        if(xhr.status === 200){
            //处理结果
            console.log(xhr.status);//状态码
            console.log(xhr.statusText);//状态字符串
            console.log(xhr.getAllResponseHeaders);//所有响应头
            console.log(xhr.response);//响应体
        }
    }
}
```

post请求

```js
const xhr = new XMLHttpRequest();
xhr.open('POST', 'http://127.0.0.1:8000/server');
xhr.send('a=100&b=100');
xhr.onreadystatechang = function(){
    if(xhr.readyState === 4){
        if(xhr.status === 200){
           //接收json的值
           let data = JSON.parse(xhr.response);
        }
    }
}
```

