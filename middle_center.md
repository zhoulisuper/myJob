> 好基友 line-height、vertical-align 和第三者幽灵空白节点的爱恨情仇

```
<div class="container">
  <div class="dialog">自适应弹出层</div>
</div>
<style>
.container{
  position: fixed;
  top: 0; right: 0; bottom: 0; left: 0;
  background-color: rgba(0, 0, 0, .15);
  text-align: center;
  font-size: 0;
  white-space: nowrap;
  overflow: auto;
}
.container:after{
  content: '';
  display: inline-block;
  height: 100%;
  vertical-align: middle;
}
.dialog{
  display: inline-block;
  width: 400px;
  height: 400px;
  vertical-align: middle;
  text-align: left;
  font-size: 14px;
  white-space: normal;
  background: white;
}
</style>
```

> 另外五种

```
1.transform
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
2.flex
    display: flex;//flex布局
    justify-content: center;//使子项目水平居中
    align-items: center;//使子项目垂直居中
3. table
    display: table-cell;
    vertical-align: middle;//使子元素垂直居中
    text-align: center;//使子元素水平居中
4.write-mode
.wp {
    writing-mode: vertical-lr;
    text-align: center;
}
.wp-inner {
    writing-mode: horizontal-tb;
    display: inline-block;
    text-align: center;
    width: 100%;
}
.box {
    display: inline-block;
    margin: auto;
    text-align: left;
}
5.grid
    .wp {
        display: grid;
    }
    .box {
        align-self: center;
        justify-self: center;
    }

```
