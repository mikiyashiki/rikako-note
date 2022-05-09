# js canvas
- ## canvas api
  - 获取canvas元素：
    ```javascript
    let canvas=document.querySelector(canvas_name)
    ```
  - 获取canvas上下文：
    ```javascript
    // 获取2d上下文，之后可以通过ctx操作canvas元素
    let ctx=canvas.getContext("2d")
    ```
  - 填充矩形：
    ```javascript
    ctx.fillStyle='rgb(255,0,0)'
    ctx.fillRect(0,0,100,100)
    ```
    - 如果画了多个图形，那么按照画的顺序，后画的元素将会覆盖在先画的图形之上
    - 通过为ctx.fillStyle指定rgba，可以画半透明的元素，alpha的值为0～1,值越接近0越透明
  - 只画矩形轮廓
    ```javascript
    ctx.strokeStyle='rgb(255,0,0)';
    ctx.strokeRect(x,y,width,height);

    // 默认情况下轮廓宽度是1px，但是可以通过ctx.lienWidth来调整宽度
    ctx.lineWidth=5
    ```
  - 画Path：画Path主要可以通过如下api
    - ctx.beginPath：在当前所处的位置开始path
    - ctx.moveTo：移动当前位置到指定位置，而不记录移动的线条
    - ctx.lineTo：在path中记录线条
    - ctx.fill：通过先前的path创建fill图形
    - ctx.stroke：通过先前的path创建stroke图形
  - 画圆形：通过ctx.arc画圆形
    ```javascript
    // arc有6个参数，分别如下
    //  1～2： 圆心的x和y坐标
    //  3：半径的像素
    //  4～5：开始的角度和结束的角度（弧度制）
    //  6：是否按逆时针画
    // arc的0度是水平向右
    ctx.arc(100,100,100,0,2*Math.Pi,true)
    // 默认情况下0度位于水平向右，但是角度增长默认是顺时针方向，在没有指定第六个参数时其默认也是顺时针方向绘制（第六个参数为false）。但是可以指定第六个参数为true，指定为true会逆时针方向绘制
    // 当path不是封闭图形时，调用fill时会将开始位置和结束位置连接后填充
    ```
  - 在canvas中绘制text文本
    - 可以通过ctx.fillText来绘制填充文本
    - 可以通过ctx.strokeText来绘制文本边框
    - 可以通过ctx.font来修改字体的种类和大小
    ```javascript
    // fillText接受3个参数
    //  1.想要绘制的文本
    //  2.绘制文本的开始x位置
    //  3.绘制文本的开始y位置
    //  绘制文本的开始位置是左下角位置，并不是左上角
    ctx.fillText(str,x,y)
    ```
  - 在canvas中绘制图片
    - 可以通过ctx.drawImage在canvas中绘制图片，其接受如下参数：
      - 一个image对象的引用
      - 想要绘制的图片的裁剪部分左上角的x坐标和y坐标
      - 想要绘制的图片的裁剪部分的width和height
      - 想要将裁剪部分绘制在canvas上的位置，相对于（0,0）的x和y坐标
      - 指定想要绘制裁剪部分的width和height，可以与裁剪部分的width和height相同，也可以将裁剪部分resize
    ```javascript
    ctx.drawImage(image,image_x,image_y,image_width,image_height,0,0,image_x,image_y)
    ```
  - 在canvas中创建动画：
    - 常用的api：
      - ctx.translate: 移动canvas和其origin point，水平方向移动x像素，垂直方向移动y单位
      - ctx.rotate：旋转整个canvas，以origin point为中心
    - 创建动画的api：
      - window.requestAnimationFrame(func)来令动画以特定的帧率刷新（60帧/s）
      - 通过向requestAnimationFrame来传递一个函数刷新canvas，并且func中继续调用requestAnimationFrame来获取下一帧
      - 当不想继续动画时，可以不再在func中调用requestAnimationFrame，或者可以调用cancelAnimationFrame来停止获取下一帧动画
