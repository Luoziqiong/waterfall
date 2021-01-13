## 关于瀑布流实现的几种方法



### 一、使用 CSS Flex 布局实现

```html
<div class="container">
  <div class="box box_sm">1</div>
  <div class="box box_xl">2</div>
  <div class="box box_s">3</div>
  <div class="box box_m">4</div>
  <div class="box box_xl">5</div>
  <div class="box box_m">6</div>
  <div class="box box_l">7</div>
  <div class="box box_s">8</div>
  <div class="box box_m">9</div>
  <div class="box box_xl">10</div>
</div>
```

```css
.container {
  width: 600px;
  height: 300px;
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
}
.box {
  width: 100px;
  margin-bottom: 10px;
}

.box_sm {
  height: 60px;
  background: aquamarine;
}

.box_m {
  height: 90px;
  background: beige;
}

.box_s {
  height: 70px;
  background: #2ce7af;
}

.box_l {
  height: 100px;
  background: rgb(248, 160, 45);
}

.box_xl {
  height: 150px;
  background: rgb(222, 184, 135);
}
```

我们会发现虽然我们实现了近似瀑布流的效果，但是首先他的排列顺序却不是我们想要的。其次需要分页加载的效果时，也不符合我们想要的效果。

当然对于某些不是很在乎排列顺序的列表来说，此种方案也算适用，但是此方案却不可完全称之为瀑布流；

### 二、 multi 方式

通过 Multi-columns 相关的属性 column-count、column-gap 配合 break-inside 来实现瀑布流布局。

```css
.container {
  -moz-column-count: 5;
  -webkit-column-count: 5;
  column-count: 5;
  -moz-column-gap: 2em;
  -webkit-column-gap: 2em;
  column-gap: 2em;
  width: 600px;
}

.box {
  -moz-page-break-inside: avoid;
  -webkit-column-break-inside: avoid;
  break-inside: avoid;
}
```

该方案的实现情况也同第一种，排列顺序是竖向排序。

### 三、使用 JS 代码实现

使用绝对定位实现, 在页面加载的时候计算各个块的位置。

```html
<div class="container" id="">
  <div class="box box_sm">1</div>
  <div class="box box_xl">2</div>
  <div class="box box_s">3</div>
  <div class="box box_m">4</div>
  <div class="box box_xl">5</div>
  <div class="box box_m">6</div>
  <div class="box box_l">7</div>
  <div class="box box_s">8</div>
  <div class="box box_m">9</div>
  <div class="box box_xl">10</div>
</div>

```

```css
.container {
  width: 600px;
  position: relative;
}
.box {
  position: absolute;
}

```

```javascript
window.onload = function () {
  const containerEl = document.getElementById("container");
  const elements = document.getElementsByClassName("box");
  waterFall(containerEl, elements, 10);
};
function waterFall(containerEl, elements, gap = 0) {
  const containerWidth = containerEl.offsetWidth;
  const elementWidth = elements[0].offsetWidth;
  const col = Math.floor(containerWidth / elementWidth); // 计算出列数

  const heightArr = [],
    len = elements.length;
  for (let i = 0; i < len; i++) {
    let el = elements[i];
    if (i < col) {
      // 第一行
      heightArr.push(el.offsetHeight);
      el.style.top = 0;
      el.style.left = `${(elementWidth + gap) * i}px`;
    } else {
      // 求出最矮盒子的高度
      let minElHeight = Math.min.apply(this, heightArr);
      // 求出最矮盒子的索引
      let minElIndex = heightArr.indexOf(minElHeight);
      el.style.top = `${minElHeight + gap}px`;
      el.style.left = `${(elementWidth + gap) * minElIndex}px`;
      // 更新最小高度
      heightArr[minElIndex] += el.offsetHeight;
    }
  }
}
```
该方案可以保证每列的长度基本比较均匀