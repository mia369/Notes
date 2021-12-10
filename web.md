# JavaWeb

----

## AJAX

- 快速使用

  ````javascript
  //1. 创建 XMLHttpRequest 对象
  var xhttp;
  if (window.XMLHttpRequest) {
      xhttp = new XMLHttpRequest();
      } else {
      // code for IE6, IE5
       xhttp = new ActiveXObject("Microsoft.XMLHTTP");
  }
  
  //2. 向服务器发送请求
  xhttp.open("GET", "url");
  xhttp.send();
  
  //3. 获取服务器响应数据
  xhttp.onreadystatechange = function() {
      if (this.readyState == 4 && this.status == 200) {
          //响应的字符串数据
          xhttp.responseText;
     }
  };
  ````

## AXIOS

对AJAx的封装

- 快速使用

  1. 创建[axios.min.js](https://github.com/axios/axios/blob/master/dist/axios.min.js)文件

  2. 在html导入axios.min.js文件, 发送请求, 获取响应

     ````html
     <script src="js/axios.js"></script>
     
     <!-- 方式一 -->
     <script>
         axios({
             method: "get",
             url: "http://localhost:8080/CookieDemo/axiosServlet?username=zhangsan"
         }).then(function (resp) {
             alert(resp.data);
         });
     </script>
     
     <script>
         axios({
             method: "post",
             url: "http://localhost:8080/CookieDemo/axiosServlet",
             data: "username=lisi"
         }).then(function (resp) {
             alert(resp.data);
         });
     </script>
     
     <!-- 方式二 -->
     <script>
         axios.get("http://localhost:8080/CookieDemo/axiosServlet?username=111").then(
             function (resp) {
                 alert(resp.data);
             }
         )
     </script>
     
     <script>
         axios.post("http://localhost:8080/CookieDemo/axiosServlet","username=111").then(
             function (resp) {
                 alert(resp.data);
             }
         )
     </script>
     ````

     

## JSON

- JSON数据和Java对象转换

  1. 导入坐标

     ````xml
     <!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
     <dependency>
         <groupId>com.alibaba</groupId>
         <artifactId>fastjson</artifactId>
         <version>1.2.78</version>
     </dependency>
     ````

  2. 转换演示

     ````java
     //Java对象转JSON字符串
     String jsonStr = JSON.toJSONString(obj);
     //JSON字符串转Java对象
     User user = JSON.parseObject(jsonStr, User.class);

