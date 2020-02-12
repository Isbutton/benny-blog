### ios下滚动问题
#### 使用iscroll: input内光标会跟随页面滚动而移动，且不复原

在滚动结束时，重绘dom。
若是因为键盘弹出导致光标的移动，在ios可以监听节点的focus，blur事件。 input focus时键盘弹起。收起键盘时blur。

```
onScrollEnd: function () {
	var node = document.activeElement; //当前focus的dom元素
	setTimeout(function () {
		if (node) {
			if (node && (node.nodeName == "TEXTAREA" || node.nodeName == 'INPUT') { 
                //如果是input或textarea
				if (node.style.textShadow === '') {
                    //改变某个不可见样式，触发dom重绘
					node.style.textShadow = 'rgba(0,0,0,0) 0 0 0'; 
				} else {
						node.style.textShadow = '';
				}
			}
		}
	},100)
}
```

#### 使用iscroll: 在小屏手机里，弹起键盘后，顶部header被顶上去，且无法还原。
在input输入框失去焦点的钩子中设置滚动到原有位置(document.body.scrollTop = document.body.scrollTop)，触发浏览器的重绘，使的错误的渲染回复正常，滚动位置也不会有改变，没有影响体验。
```
blurInput: function(parent){
	if(U.ua.iOS) {
		document.body && (document.body.scrollTop = document.body.scrollTop)
	}
}
```
#### 使用iscroll: 当手势滑出屏幕外滚动区域不会弹
上拉时，当手指滑动幅度超过屏幕，无法触发touchend事件，回弹动画无法执行，页面无法回弹。
在滑动期间判断当接近屏幕边缘手动触发回弹。



| 参数名              | 说明                                               | 可选值                | 默认值                               |
| ------------------- | -------------------------------------------------- | --------------------- | ------------------------------------ |
| onBeforeScrollStart | 开始滚动前的事件回调<br />默认是阻止浏览器默认行为 |                       | function (e) { e.preventDefault(); } |
| onRefresh           | refresh 的回调                                     | `Function` 自定义函数 |                                      |
| onScrollStart       | 开始滚动的回调                                     | `Function` 自定义函数 |                                      |
| onBeforeScrollMove  | 在内容移动前的回调                                 | `Function` 自定义函数 |                                      |
| onBeforeScrollEnd   | 在滚动结束前的回调                                 | `Function` 自定义函数 |                                      |
| onScrollMove        | 内容移动的回调                                     | `Function` 自定义函数 |                                      |
| onScrollEnd         | 在滚动完成后的回调                                 | `Function` 自定义函数 |                                      |
| onTouchEnd          | 手离开屏幕后的回调                                 | `Function` 自定义函数 |                                      |
| onDestroy           | 销毁实例的回调                                     | `Function` 自定义函数 |                                      |



```
this.y是页面已经滚动的垂直距离，this.maxScrollY是最大垂直滚动距离，this.pointY手指当前的垂直坐标。


onScrollMove: function () {  
 if((this.y < this.maxScrollY) && (this.pointY < 1)){  
  this.scrollTo(0, this.maxScrollY, 400);  
  return;  
 } else if (this.y > 0 && (this.pointY > window.innerHeight - 1)) {  
  this.scrollTo(0, 0, 400);  
  return;  
 }  

 ......  
}  

onBeforeScrollMove : function(e){
	e.preventDefault();
	if (this.pointY < 1 && U.dom.hasClass(U.$('#storeScrollCf .mod-search-bar'), 'mod-search-bar_active') && U.$("#storeScrollCf .resultList").innerHTML === '') {
		this.scrollTo(0, 0, 400); 
	}
}
```

#### 软键盘收起后底部空白
在input输入框失去焦点的钩子中
```
$('.info-input').blur(function(){
   resolveBug();
})
 //解决软键盘收起后的底部空白
function resolveBug( e ){
    setTimeout(function(){
        if(document.activeElement.tagName == 'INPUT' || document.activeElement.tagName == 'TEXTAREA'){
            return
        }
        let result = 'pc';
        if(/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) { //判断iPhone|iPad|iPod|iOS
            result = 'ios'
        }else if(/(Android)/i.test(navigator.userAgent)) {  //判断Android
            result = 'android'
        }
        if( result === 'ios' ){
            window.scrollTo(0,0);
        }
    },10)
}
```

#### iscroll下问题参考
https://www.cnblogs.com/yexiaochai/p/3764503.html