@[toc](javascript移动设备触屏画板（附演示地址和源码）)

# 一、前言
上篇学习了 [《javascript移动设备触屏事件及练习》](https://blog.csdn.net/iamlujingtao/article/details/102563600)后，我们初步了解了触屏事件。
这次我们这基础上打造一款有触摸功能的画板，好让我们的艺术细胞发扬广大。
触摸事件我们用到“touchstart、touchmove、touchend”，
而画板我们用到html5的“canvas”，二者结合起来就能打造简单好用的画板了，动动你的手指作画吧~

关于**canvas**这里有详细的解释：[http://www.cnblogs.com/tim-li/archive/2012/08/06/2580252.html](http://www.cnblogs.com/tim-li/archive/2012/08/06/2580252.html)

# 二、在线演示和GitHub源码
注意：
- 演示地址下面有二维码，可以手机查看。
- 画画流不流畅很多因素，不同手机软硬件不同，同一手机不同浏览器也有不同的流程效果。

效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191015120316251.gif)

[–>在线演示](https://lujingtao.github.io/Javascript-Touchscreen-Sketchpad/)
[–>GitHub源码](https://github.com/lujingtao/Javascript-Touchscreen-Sketchpad)

# 三、示例核心代码

```js
		(function(){ 
			var isTouchPad = (/hp-tablet/gi).test(navigator.appVersion);
			var hasTouch = 'ontouchstart' in window && !isTouchPad;
			var touchStart = hasTouch ? 'touchstart' : 'mousedown';
			var touchMove = hasTouch ? 'touchmove' : 'mousemove';
			var touchEnd = hasTouch ? 'touchend' : 'mouseup';
			var startX = 0;
			var startY = 0;
			var penWidth = 1;
			var penColor = "#000";
			
			
			//通用函数
			var obj=function(id){ return document.getElementById(id) }

			var addClass =function(ele, className){
				 if (!ele || !className || (ele.className && ele.className.search(new RegExp("\\b" + className + "\\b")) != -1)) return;
				 ele.className += (ele.className ? " " : "") + className;
			}

			var removeClass = function(ele, className){
				 if (!ele || !className || (ele.className && ele.className.search(new RegExp("\\b" + className + "\\b")) == -1)) return;
				 ele.className = ele.className.replace(new RegExp("\\s*\\b" + className + "\\b", "g"), "");
			}


			//创建canvas
			canvT = obj("content").offsetTop;
			canvL = obj("content").offsetLeft;

			var canvas = obj("canvas");
			canvas.width  = document.body.clientWidth;
			canvas.height  = document.body.clientHeight - canvT;

			var context = canvas.getContext("2d");
			
	
			//绘画
			var draw = function( x,y ){
				context.lineTo(x-canvL, y-canvT);
				context.lineWidth = penWidth;
				context.strokeStyle = penColor; 
				context.lineCap = "round";
				context.stroke();
			}

			//圆点
			var circlePoint = function( x,y ){
			   context.beginPath();
			   context.arc(x-canvL, y-canvT, penWidth/2, 0, Math.PI * 2, true);
			   context.closePath();
			   context.fillStyle = penColor;
			   context.fill();
			}

			//pen事件
			var pens = obj("pen").getElementsByTagName("li");
			for( var i=0; i<pens.length; i++ ){
				(function(){
					pens[i].addEventListener('click', function(){
						penWidth = this.innerText;
						for ( var j=0 ; j<pens.length ; j++ ){
							removeClass( pens[j],'cur' )
						}
						addClass(this,'cur');

					});
				})()
			}

			//color事件
			var color=obj("color");
			var curColor=obj("curColor");
			var selectColor = obj("selectColor");
			var colors = obj("color").getElementsByTagName("li");
			for ( var i=0; i<colors.length ; i++ ){	
				colors[i].style.backgroundColor=colors[i].innerText;
				(function(){
					colors[i].addEventListener('click', function(){
						penColor = curColor.style.backgroundColor=this.innerText;
						selectColor.style.display="none";
					});
				})()
			}
			curColor.addEventListener('click', function(){ selectColor.style.display="block"; })


			//reset事件
			obj("reset").addEventListener('click', function(){
				context.clearRect(0,0,document.body.clientWidth,canvas.height);
			});


			//触摸事件
			var start = function(e){
				var point = hasTouch ? e.touches[0] : e;
				startX = point.pageX;
				startY = point.pageY;
				context.beginPath();
				
				//添加“触摸移动”事件监听
				canvas.	addEventListener(touchMove, move,false);
				//添加“触摸结束”事件监听
				canvas.addEventListener(touchEnd, end ,false);
			}

			var move = function(e){
					var point = hasTouch ? e.touches[0] : e;
					e.preventDefault();
					draw( point.pageX,point.pageY );
			}

			var end = function(e){
					var point = hasTouch ? e.touches[0] : e;
					if( point.pageX == startX && point.pageY == startY ){ circlePoint( startX,startY ) }
					
					canvas.removeEventListener(touchStart, end, false);
					canvas.removeEventListener(touchMove, move, false);
					canvas.removeEventListener(touchEnd, end, false);
			}

			//添加“触摸开始”事件监听
			canvas.addEventListener(touchStart,start,false);


		})();
```
