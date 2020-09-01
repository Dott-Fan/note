#### jQuery Validate 表单验证

##### 导入js库(cdn版)

```javascript
//核心库
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/lib/jquery.js"></script>
//汉化包
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/dist/jquery.validate.min.js"></script>
```

##### 默认校验规则

| 序号 | 规则               | 描述                                                         |
| :--- | :----------------- | :----------------------------------------------------------- |
| 1    | required:true      | 必须输入的字段。                                             |
| 2    | remote:"check.php" | 使用 ajax 方法调用 check.php 验证输入值。                    |
| 3    | email:true         | 必须输入正确格式的电子邮件。                                 |
| 4    | url:true           | 必须输入正确格式的网址。                                     |
| 5    | date:true          | 必须输入正确格式的日期。日期校验 ie6 出错，慎用。            |
| 6    | dateISO:true       | 必须输入正确格式的日期（ISO），例如：2009-06-23，1998/01/22。只验证格式，不验证有效性。 |
| 7    | number:true        | 必须输入合法的数字（负数，小数）。                           |
| 8    | digits:true        | 必须输入整数。                                               |
| 9    | creditcard:        | 必须输入合法的信用卡号。                                     |
| 10   | equalTo:"#field"   | 输入值必须和 #field 相同。                                   |
| 11   | accept:            | 输入拥有合法后缀名的字符串（上传文件的后缀）。               |
| 12   | maxlength:5        | 输入长度最多是 5 的字符串（汉字算一个字符）。                |
| 13   | minlength:10       | 输入长度最小是 10 的字符串（汉字算一个字符）。               |
| 14   | rangelength:[5,10] | 输入长度必须介于 5 和 10 之间的字符串（汉字算一个字符）。    |
| 15   | range:[5,10]       | 输入值必须介于 5 和 10 之间。                                |
| 16   | max:5              | 输入值不能大于 5。                                           |
| 17   | min:10             | 输入值不能小于 10。                                          |

##### 校验规则书写

```javascript
// 在键盘按下并释放及提交后验证提交表单
  $("#signupForm").validate({
    rules: {
      firstname: "required",
      lastname: "required",
      username: {
        required: true,
        minlength: 2
      },
      password: {
        required: true,
        minlength: 5
      },
      confirm_password: {
        required: true,
        minlength: 5,
        equalTo: "#password"
      },
      email: {
        required: true,
        email: true
      },
      topic: {
        required: "#newsletter:checked",
        minlength: 2
      },
      agree: "required"
    },
    messages: {//中文提示信息可以自定义
      firstname: "请输入您的名字",
      lastname: "请输入您的姓氏",
      username: {
        required: "请输入用户名",
        minlength: "用户名必需由两个字符组成"
      },
      password: {
        required: "请输入密码",
        minlength: "密码长度不能小于 5 个字符"
      },
      confirm_password: {
        required: "请输入密码",
        minlength: "密码长度不能小于 5 个字符",
        equalTo: "两次密码输入不一致"
      },
      email: "请输入一个正确的邮箱",
      agree: "请接受我们的声明",
      topic: "请选择两个主题"
     }
    })
```

##### 常用方法

##### 1、用其他方式替代默认的 SUBMIT

```javascript
 $("#signupForm").validate({
        submitHandler:function(form){
            alert("提交事件!");   
            form.submit();
        }    
  });
```

使用 ajax 方式

```
 $(".selector").validate({     
 submitHandler: function(form) 
   {      
      $(form).ajaxSubmit();     
   }  
 }) 
```

可以设置 validate 的默认值，写法如下：

```
$.validator.setDefaults({
  submitHandler: function(form) { alert("提交事件!");form.submit(); }
});
```

如果想提交表单, 需要使用 form.submit()，而不要使用 $(form).submit()

###### 2、debug，只验证不提交表单

如果这个参数为true，那么表单不会提交，只进行检查，调试时十分方便。

```javascript
$().ready(function() {
 $("#signupForm").validate({
        debug:true
    });
});
```

如果一个页面中有多个表单都想设置成为 debug，则使用：

```javascript
$.validator.setDefaults({
   debug: true
})
```

###### 3、每个字段验证通过执行函数

```javascript
success：String,Callback
```

要验证的元素通过验证后的动作，如果跟一个字符串，会当作一个 css 类，也可跟一个函数。

```javascript
success: function(label) {
    // set &nbsp; as text for IE
    label.html("&nbsp;").addClass("checked");
    //label.addClass("valid").text("Ok!")
}
```

添加 "valid" 到验证元素，在 CSS 中定义的样式 <style>label.valid {}</style>。

```javascript
success: "valid"
```

###### 4、异步验证

```
remote：URL
```

使用 ajax 方式进行验证，默认会提交当前验证的值到远程地址，如果需要提交其他的值，可以使用 data 选项。

```
remote: "check-email.php"
remote: {
    url: "check-email.php",     //后台处理程序
    type: "post",               //数据发送方式
    dataType: "json",           //接受数据格式   
    data: {                     //要传递的数据
        username: function() {
            return $("#username").val();
        }
    }
}
```

远程地址只能输出 "true" 或 "false"，不能有其他输出。

###### 5、添加自定义校验

```
addMethod：name, method, message
```

自定义验证方法

```javascript
// 中文字两个字节
jQuery.validator.addMethod("byteRangeLength", function(value, element, param) {
    var length = value.length;
    for(var i = 0; i < value.length; i++){
        if(value.charCodeAt(i) > 127){
            length++;
        }
    }
  return this.optional(element) || ( length >= param[0] && length <= param[1] );   
}, $.validator.format("请确保输入的值在{0}-{1}个字节之间(一个中文字算2个字节)"));

// 邮政编码验证   
jQuery.validator.addMethod("isZipCode", function(value, element) {   
    var tel = /^[0-9]{6}$/;
    return this.optional(element) || (tel.test(value));
}, "请正确填写您的邮政编码");
```

#### Object.defineproperty和 Proxy 两种用法?

##### Object.defineProperty() 

第一个参数要劫持的对象，
第二个参数是要定义或修改的属性名称
第三个是对定义或修改的属性描述 有get()和set()两个方法

```javascript
var userinfo={
	name:'我是mvvm的双向绑定'
}
var obj={} //定义被劫持的对象
Object.defineProperty(obj,'name',{
	get(){
	//获取信息时执行
	return userinfo.name
	},
	set(data){
	//修改数据时执行
	userinfo.name=data            	  		          			document.querySelector('#msg').innerHTML=data 
	//更新dom
	return true
	}
})
var userinfo={
	name:'我是mvvm的双向绑定'
	}
var obj={} //定义被劫持的对象
Object.defineProperty(obj,'name',{
	get(){
	//获取信息时执行
	return userinfo.name
	},
	set(data){
	//修改数据时执行
	userinfo.name=data
    document.querySelector('#msg').innerHTML=data //更新dom
	return true
	}
})
//渲染数据
document.querySelector('#msg').innerHTML=obj.name  
//一开始是获取用户信息时执行get方法
function  change(e){
	let value=e.value;
	obj.name=value;   //修改数据时执行set方法
	return true
}
```

Proxy: 语法 let proxy=new Proxy(target,handler)

​        //target 是目前对象你要进行处理的对象 

​        //handler 相当一个容器。里面存放无数可以操作对象属性的方法 ，就是就你操作对象的一些描述

​        let target={

​            a:1,

​            b:2

​        }

​        let proxy=new Proxy(target,{

​            //访问数据时执行

​            get(target,prop){ 

​                //参数target就是你处理的函数，prop是你处理函数的属性

​                return 'This.is property value' +target[prop]

​            },

​            set(target,prop,value){

​                //参数target就是你处理的函数，prop是你处理函数的属性,value就是你设置的值

​                target[prop]=value

​                console.log( target[prop])

​            }

​        })

​        //访问target 对象里面的值  

​        console.log(proxy.a) //打印：This.is property value 1

​        //修改target 对象里面的值

​        proxy.b=3       //打印：target[prop] 值是 3