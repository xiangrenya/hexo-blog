---
layout: post
title:  "用 Canvas 实现签名"
date:   2019-7-17
categories: 前端
tags: canvas
comments: true
---

## 代码片段

```javascript
function signature(canvasId){
    let x = 0;
    let y = 0;

    const whiteBoard = document.getElementById(canvasId);
    const context = whiteBoard.getContext('2d');
    const rect = whiteBoard.getBoundingClientRect();

    whiteBoard.addEventListener('mousedown', e => {
        x = e.clientX - rect.left;
        y = e.clientY - rect.top;
        isDrawing = true;
        whiteBoard.addEventListener('mousemove', mouseMoveEvent)
    });

    whiteBoard.addEventListener('mouseup', e => {
        x = 0;
        y = 0;
        whiteBoard.removeEventListener('mousemove', mouseMoveEvent);
    });

    function mouseMoveEvent(e){
        drawLine(context, x, y, e.clientX - rect.left, e.clientY - rect.top);
        x = e.clientX - rect.left;
        y = e.clientY - rect.top;
    }

    function drawLine(context, x1, y1, x2, y2) {
        context.beginPath();
        context.strokeStyle = 'black';
        context.lineWidth = 1;
        context.moveTo(x1, y1);
        context.lineTo(x2, y2);
        context.stroke();
        context.closePath();
    }
}
```

## 示例效果

![signature](/assets/images/201907/signature.gif)

线上预览：[Drawing with mouse events](https://codepen.io/xiangrenya/pen/gNybMz)