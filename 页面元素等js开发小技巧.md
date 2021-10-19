#### 判断容器能否滚动

function eleCanScroll(ele) {
  if (!ele instanceof HTMLElement) {
    console.log("fuck off");
    return;
  }
  if (ele.scrollTop > 0) {
    return true;
  } else {
    ele.scrollTop++;
    // 元素不能滚动的话，scrollTop 设置不会生效，还会置为 0
    const top = ele.scrollTop;
    // 重置滚动位置
    top && (ele.scrollTop = 0);
    return top > 0;
  }
}


#### 是否scroll滚动结束

var scrollTimer
const timeout = 400
function handler () {
  // ...
}
document.addEventListener('scroll', () => {
  clearTimeout(scrollTimer)
  scrollTimer = setTimeout(handler, timeout)
})
####  页面缩放
function detectZoom (){
  var ratio = 0,
    screen = window.screen,
    ua = navigator.userAgent.toLowerCase();
   
   if (window.devicePixelRatio !== undefined) {
      ratio = window.devicePixelRatio;
  }else if (~ua.indexOf('msie')) {
    if (screen.deviceXDPI && screen.logicalXDPI) {
      ratio = screen.deviceXDPI / screen.logicalXDPI;
    }
  } else if (window.outerWidth !== undefined && window.innerWidth !== undefined) {
    ratio = window.outerWidth / window.innerWidth;
  }
  
 
     
   if (ratio){
    ratio = Math.round(ratio * 100);
  }
     
   return ratio;
};
function isScale(){
  var rate = detectZoom();
  if(rate != 100){
    //如何让页面的缩放比例自动为100,'transform':'scale(1,1)'没有用，又无法自动条用键盘事件，目前只能提示让用户如果想使用100%的比例手动去触发按ctrl+0
    console.log(1)
    alert('当前页面不是100%显示，请按键盘ctrl+0恢复100%显示标准，以防页面显示错乱！')
  }
}

### 判断window对象自身
利用这个特性判断是否是 Window 对象Window 对象作为客户端 JavaScript 的全局对象，它有一个 window 属性指向自身
function isWindow( obj ) {
    return obj != null && obj === obj.window;
}

### 事件对象实例对象的判断
ele instanceof HTMLElement
e.nativeEvent instanceof     Event
if (e.target instanceof HTMLInputElement && !isOnComposition) {
    props.onChange(e)
}

### dom元素读写分离
function doubleHeight(element) {
  var currentHeight = element.clientHeight;
  window.requestAnimationFrame(function () {
    element.style.height = (currentHeight * 2) + 'px';
  });
}
elements.forEach(doubleHeight);
### 滚动的监听
页面滚动事件（scroll）的监听函数，就很适合用 window.requestAnimationFrame() ，推迟到下一次重新渲染
$(window).on('scroll', function() {
   window.requestAnimationFrame(scrollHandler);
});
### 时间分片
var start = null;
var element = document.getElementById('SomeElementYouWantToAnimate');
element.style.position = 'absolute';

function step(timestamp) {
  if (!start) start = timestamp;
  var progress = timestamp - start;
  element.style.left = Math.min(progress / 10, 200) + 'px';
  if (progress < 2000) {
    window.requestAnimationFrame(step);
  }
}

window.requestAnimationFrame(step);