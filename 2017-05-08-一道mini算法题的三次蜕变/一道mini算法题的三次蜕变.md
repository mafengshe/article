# 一道mini算法题的三次蜕变

今天，码蜂社大群里的一位JS初学小伙伴提出了一个问题

![](./images/question.png)

这是ta在学习JS过程中自己做练习遇到的问题，自己也写了一个版本，后来通过码蜂社的Jsbin分享到了群里，代码如下：
```	function getRGB(sixteenValue){
		console.log(sixteenValue);
		var i , arr = [];
		for(i = 1 ; i < sixteenValue.length ; i+= 2){
			for(var j =2 ; j < sixteenValue.length ; j += 2){
				if( i == 1 && j == 2){
					if(isNaN(parseInt(sixteenValue[i]))){ 
						//如果parseInt()返回NaN，则用十六进制转换
						var a1 = parseInt(sixteenValue[i],16);
					}else{
						//否则用十进制转换
						var a1 = parseInt(sixteenValue[i],10);
					}
					if(isNaN(parseInt(sixteenValue[j]))){
						var a2 = parseInt(sixteenValue[j],16);
					}else{
						var a2 = parseInt(sixteenValue[j],10);
					}
					arr[0] = a1*a2;
				}
				if( i == 3 && j == 4){
					if(isNaN(parseInt(sixteenValue[3]))){
						var a3 = parseInt(sixteenValue[3],16);
					}else{
						var a3 = parseInt(sixteenValue[3],10);
					}
					if(isNaN(parseInt(sixteenValue[4]))){
						var a4 = parseInt(sixteenValue[4],16);
					}else{
						var a4 = parseInt(sixteenValue[4],10);
					}
					arr[1] = a3*a4;
				}

				if( i == 5 && j == 6){
					if(isNaN(parseInt(sixteenValue[5]))){
						var a5 = parseInt(sixteenValue[5],16);
					}else{
						var a5 = parseInt(sixteenValue[5],10);
					}
					if(isNaN(parseInt(sixteenValue[6]))){
						var a6 = parseInt(sixteenValue[6],16);
					}else{
						var a6 = parseInt(sixteenValue[6],10);
					}
					arr[2] = a5*a6;
				}
			}
		}
		return "rgb(" + arr[0] + "," +arr[1] + "," + arr[2] + ")";
	}
	var a = getRGB("#FF55F5");
	console.log(a);
```

首先声明我很欣赏初学者动手写代码的精神，毕竟很多前端初学者试图通过背书学好前端，而且这段代码是运行正确的，这名同学的学习态度是非常赞的，但是，也希望能够继续保持，本文我们主要来说说代码。

具体到这段代码，确实有点儿惨不忍睹了，一个小小的算法却用了四十多行的代码，整体看起来非常冗余和啰嗦。

于是码蜂社可爱的教辅姐姐，自己动手写了一个

![](./images/jiaofu_word.png)

代码如下：

```javascript
function getrgb(str){
  var pattern =new RegExp(/^#[0-9a-fA-F]{6}$/); 
  if(!pattern.test(str)){
    console.log("invalid hex");
    return;
  }
   var num = parseInt(str.slice(1),16);
  
   var b = num %　256;
   num = parseInt(num / 256);
   var g = num %　256;
   num = parseInt(num / 256);   
   var r = num %　256;
  
   return 'rgb('+r+","+g+","+b+")";
}
console.log(getrgb('#ababab'))
```
利用正则表达式判断参数是否合法，然后把颜色的十六进制转化成一个整数，在通过取余的方式得到对应的RGB。群里有小伙伴觉得，哎哟，不错哟！我们的教辅有点飘飘然了。

这时候Mark老师来了

![](./images/marksopinion.png)

于是就有了以下这两种版本：

```javascript
function getrgb(str){
  var pattern =new RegExp(/^#[0-9a-fA-F]{6}$/); 
  if(!pattern.test(str)){
    console.log("invalid hex");
    return;
  }
   var num = parseInt(str.slice(1),16);  
   return 'rgb('+((num >>> 16) & 255)+","+((num >>> 8) & 255)+","+(num & 255 )+")";
}
```

以上这种用位操作（写过C或C++的同学肯定不会陌生）代替了取余整除的操作，非常简洁。

```javascript
function getrgb(str){
  var pattern =new RegExp(/^#([0-9a-fA-F]{2})([0-9a-fA-F]{2})([0-9a-fA-F]{2})$/); 
  var arr=str.match(pattern);
  if(!arr){
    console.log("invalid hex");
    return;
  }  
   return 'rgb('+parseInt(arr[1],16)+","+parseInt(arr[2],16)+","+parseInt(arr[3],16)+")";
}

```
以上这种用到了码蜂社《Web突破班》最近在讲的正则表达式分组的概念，直接从输入的字符串中提取到了RGB所对应的两位，并直接转换成对应的值，不禁感叹正则表达式真真是很好用呀！

这个小故事告诉我们，初学者一定要像文中这位同学一样，不要眼高手低，多练习一些JavaScript的小例子,代码不仅要能写出来、写的对，还要写的越来越漂亮哟！

![](./images/thanks.png)
