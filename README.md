# JS加密-U加密
编码式JS代码加密，将JS代码分割、Unicode编码化存储到数组中，使代码全部成为密文状态显示，执行时解密、重新编码并eval调用运行。
加密后的代码中存在大量的u字符（unicode编码），所以命名为“<b>U加密</b>”。
U加密后代码可直接运行，使用方式与功能与加密前无异。

Copyright © jshaman.com
作者：w2sft

在线使用：https://www.jshaman.com/tools/u-jiami.html
## 加密效果示例
JS源码：

```js
(function (){
    var domain = "jshaman.com";
    var from_year = 2017;
    var copyright = function(){
        return "(c)" + from_year + "-" + (new Date).getFullYear() + "," + domain;
    };
    var console_log = console.log;
    console_log(copyright())
})();
```

U加密后的JS代码：
```js
var u=[`\u0028\u0066\u0075\u006e\u0063\u0074\u0069\u006f\u006e`,`\u0028\u0029\u007b`,`\u0076\u0061\u0072`,`\u0064\u006f\u006d\u0061\u0069\u006e`,`\u0022\u006a\u0073\u0068\u0061\u006d\u0061\u006e\u002e\u0063\u006f\u006d\u0022\u003b`,`\u0066\u0072\u006f\u006d\u005f\u0079\u0065\u0061\u0072`,`\u0032\u0030\u0031\u0037\u003b`,`\u0063\u006f\u0070\u0079\u0072\u0069\u0067\u0068\u0074`,`\u0066\u0075\u006e\u0063\u0074\u0069\u006f\u006e\u0028\u0029\u007b`,`\u0072\u0065\u0074\u0075\u0072\u006e`,`\u0022\u0028\u0063\u0029\u0022`,`\u0022\u002d\u0022`,`\u0028\u006e\u0065\u0077`,`\u0044\u0061\u0074\u0065\u0029\u002e\u0067\u0065\u0074\u0046\u0075\u006c\u006c\u0059\u0065\u0061\u0072\u0028\u0029`,`\u0022\u002c\u0022`,`\u0064\u006f\u006d\u0061\u0069\u006e\u003b`,`\u0063\u006f\u006e\u0073\u006f\u006c\u0065\u005f\u006c\u006f\u0067`,`\u0063\u006f\u006e\u0073\u006f\u006c\u0065\u002e\u006c\u006f\u0067\u003b`,`\u0063\u006f\u006e\u0073\u006f\u006c\u0065\u005f\u006c\u006f\u0067\u0028\u0063\u006f\u0070\u0079\u0072\u0069\u0067\u0068\u0074\u0028\u0029\u0029`,`\u007d\u0029\u0028\u0029\u003b`];
var u2=[0,1,2,3,5,7,9,11,13,14,15,19,21,22,24,26,29,31,32,33];
var u3=`u6[0] u6[1]
u6[2] u6[3] = u6[5]
u6[2] u6[7] = u6[9]
u6[2] u6[11] = u6[13]
u6[14] u6[15] + u6[7] + u6[19] + u6[21] u6[22] + u6[24] + u6[26]
};
u6[2] u6[29] = u6[31]
u6[32]
u6[33]
`;
for(u5=0; u5<u.length; u5++){
u3 = u3.replace(new RegExp("u6\\["+u2[u5]+"\\]","g"), u[u5].replace("`","").replace("`",""));
}
window[(14).toString(32)+(31).toString(32)+(10).toString(32)+(21).toString(32)](u3);
```
## U加密源码及使用方法
```js
//U加密
//参数：
//js_code：JS源码
//hide_eval：是否隐藏eval，值为true或false
//env：执行环境，值为0时表示在Node.JS中执行，为1时在浏览器中执行
//minify：是否压缩加密后的代码，值为true或false
//返回值：加密后的JS代码
function u_encode(js_code, hide_eval, env, minify){
    //去除一行代码中的前后空格
    js_code = trimLines(js_code);
    //把代码以空格分割，放入数组
    var js_arr = js_code.trim().split(/\s+/);
    var js_obj = {};
    var js_min_arr = [];
    var js_min_arr_index = [];
    var index = 0;
    //遍历代码数组
    for(i=0; i<js_arr.length; i++){
        //长度大于3的数组内容
        if(js_arr[i].length >= 3){
            //判断对像中是否存在，用对像不用数组是因为效率更高
            if(js_obj[js_arr[i]] == null){
                index = i;
                js_obj[js_arr[i]] = i;
                //缩小的数组
                js_min_arr.push("`" + stringToUnicode(js_arr[i]).trim() + "`");
                //缩小的数组索引，解密用
                js_min_arr_index.push(index);
            }else{
                //索引，解密用
                index = js_obj[js_arr[i]];
            }
            //将代码进行替换加密
            js_code = js_code.replace(js_arr[i],"u6["+ index +"]");
        }
    }
    //代码数组
    var u = "var u=[" + js_min_arr + "];";
    //代码数组索引
    var u2 = "var u2=[" + js_min_arr_index + "];";
    //加密的代码
    var u3 = "var u3=`" + js_code + "`;";
    //解密并执行代码
    var u4 = 
    `
for(u5=0; u5<u.length; u5++){
u3 = u3.replace(new RegExp("u6\\\\["+u2[u5]+"\\\\]","g"), u[u5].replace("\`","").replace("\`",""));
}
eval(u3);
    `;
    //组合，使可以自动执行加密代码
    var e = u+"\n"+u2+"\n"+u3+u4;
    //隐藏eval
    if(hide_eval == true){
        if(env == 0){
            //nodejs环境
            e = e.replace("eval","global[(14).toString(32)+(31).toString(32)+(10).toString(32)+(21).toString(32)]");
        }else{
            //windows环境
            e = e.replace("eval","window[(14).toString(32)+(31).toString(32)+(10).toString(32)+(21).toString(32)]");
        }
    }
    //压缩，去除回车换行
    if(minify == true){
        e = e.replace(/\n/g,"");
    }
    //返回加密结果
    return (e);
}

//字符串unicode化
function stringToUnicode(str) {
    var result = [];
    for (var i = 0; i < str.length; i++) {
        var charCode = str.charCodeAt(i);
        result.push("\\u" + ("0000" + charCode.toString(16)).slice(-4));
    }
    return result.join('');
}

//使用正则表达式匹配每行开头和结尾的空格
function trimLines(text) {
    return text.replace(/^[ \t]+|[ \t]+$/gm, '');
}

//JS源码
var source = `
(function (){
    var domain = "jshaman.com";
    var from_year = 2017;
    var copyright = function(){
        return "(c)" + from_year + "-" + (new Date).getFullYear() + "," + domain;
    };
    var console_log = console.log;
    console_log(copyright())
})();
`;
console.log(source);
//U加密
var encode = u_encode(source, true, 1, true);
//加密后的代码可以直接在Node.JS或浏览器中运行
console.log(encode);
```
