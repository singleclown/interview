### 自适应
* 浮动实现自适应
浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（Margin collapsing）也只会发生在属于同一BFC的块级元素之间。
    <div style="height:200px">
        <div style="width: 200px;float: left;">123</div>
        <div style="border: 1px solid red;overflow: hidden;">fff</div>
    </div>
如果一个元素具有BFC,那么内部元素再怎么翻江倒海，翻云覆雨，都不会影响外面的元素。所以，BFC元素是不可能发生margin重叠的，因为margin重叠会影响外面的元素的；BFC元素也可以用来清楚浮动带来的影响，因为如果不清楚，子元素浮动则会造成父元素高度塌陷，必然会影响后面元素的布局和定位，这显然有违BFC元素的子元素不会影响外部元素的设定。
* 盒子实现自适应（margin,padding）