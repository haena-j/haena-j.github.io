---
title: HTML5 캔버스(Canvas)와 안티 앨리어싱(Anti-Aliasing)
tag:
- anti-aliasing
- canvas
---

## HTML5 캔버스(Canvas)와 앨리어싱(Aliasing) 현상
HTML5 캔버스는 개발자가 JavaScript를 사용하여 웹 페이지에 그래픽과 애니메이션을 그릴 수 있는 웹 브라우저의 기능이다. 
캔버스에 그릴 때 발생할 수 있는 한 가지 문제는 모양의 가장자리가 들쭉날쭉하거나 '앨리어싱'되어 나타날 수 있다는 것이다. 
보통 해상도가 높은 모니터에서 곡선이 깨져보이거나 텍스트가 흐리게 보이는 현상으로 알 수 있다.

원인은 캔버스가 모양을 표현하기 위해 고정된 수의 픽셀을 사용하고 디스플레이마다 이를 표현하는게 다르기 때문이다.

조금 더 자세히 알아보자면, DPR에 대해 알아야 한다.

## DPR
DPR은 "Device Pixel Ratio"의 약자로, 물리적 픽셀(physical pixel 또는 디바이스 픽셀)과 논리적 픽셀(logical pixel 또는 CSS 픽셀)의 비율을 나타내는 값이다.

### 물리적 픽셀과 논리적 픽셀
물리적 픽셀은 디바이스가 실제로 표현할 수 있는 물리적인 화소(pixel) 기본 단위이며, 논리적 픽셀은 css에서 웹 페이지 상의 요소의 크기를 측정하는 단위로 사용된다.
모니터가 얼마나 많은 픽셀을 표현하는지를 화면 해상도(display resolution)라고 부르며 물리적인 픽셀로 처리한다.

DPR은 물리적 픽셀 대 논리적 픽셀의 비율이며 웹 브라우저에서 장치 화면의 해상도와 일치하도록 웹 페이지의 콘텐츠 크기를 조정하는 방법을 결정하는 데 사용된다.
(좀 더 알아보고자 한다면, 레티나 디스플레이 등장과 관련해서 서칭해보면 좋다.)

디바이스의 해상도가 높을 수록 높은 DPR을 가지게되는데,
장치의 DPR이 2인 경우 물리적 픽셀이 논리적 픽셀보다 두 배 많다는 의미이므로 브라우저는 웹 페이지 콘텐츠를 2배로 확장해야 디바이스에 제대로 표시될 것이다.

예를들어, 만약  크기가 100px(CSS 픽셀) 인 이미지를 200px의 디바이스에서 보여주게 되면, 100px인 이미지를 200px로 채우게(늘리게)되고 이미지가 깨지거나 흐리게 된다.

캔버스에 그릴 때 앨리어싱을 수정하거나 줄이는 데 사용할 수 있는 몇 가지 기술이 있다.

## 안티 앨리어싱(Anti-Aliasing)
안티 앨리어싱은 위에서 설명한 캔버스 모양의 들쭉날쭉한 가장자리를 부드럽게 만드는 데 사용되는 기술이다.

앨리어싱을 단순하게 해결할 수 있는 방법 중 하나는, 미디어쿼리와 device-pixel-ratio 를 통해 화면에 따라 이미지의 해상도를 조절할 수 있다.

아래는 스크립트를 통해 해결하는 방법들이다.
###  devicePixelRatio 속성과 context.setTransform() 메서드 
context.setTransform() 메서드를 사용하여 소수 값으로 캔버스 크기를 조정하는 방법이 있다. 
그러면 고해상도 이미지가 생성된 다음 크기가 줄어들어 가장자리가 더 부드러워진다.

예를 들어 아래와 같이 devicePixelRatio 속성을 사용하여 장치의 현재 DPR을 가져오고 그에 따라 캔버스 크기를 조정하는 방법이 있다.
```javascript
<canvas id="myCanvas"></canvas>

<script>
  var canvas = document.getElementById("myCanvas");
  var ctx = canvas.getContext("2d");
  var ratio = window.devicePixelRatio || 1; 
  ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
  ctx.lineWidth = 1; // Set the line width
  ctx.strokeRect(10, 10, 50, 50); // Draw a rectangle with anti-aliasing
</script>
```

### context.imageSmoothingQuality 속성
context.imageSmoothingQuality 속성을 이용하는 방법도 있다. 

imageSmoothingEnabled = true 를 해주어야 하고,  imageSmoothingQuality에는 "low", "medium", "high" 값이 올 수 있다.

참고로, 파이어폭스와 IE를 지원하지 않는다.

```javascript
<canvas id="myCanvas"></canvas>

<script>
  var canvas = document.getElementById("myCanvas");
  var ctx = canvas.getContext("2d");
  ctx.imageSmoothingEnabled = true; // Enable anti-aliasing
  ctx.imageSmoothingQuality = "high";
  ctx.lineWidth = 1; // Set the line width
  ctx.strokeRect(10, 10, 50, 50); // Draw a rectangle with anti-aliasing
</script>

```




참고문서
- https://en.wikipedia.org/wiki/Aliasing
- https://tomroth.com.au/dpr/
- https://ui.toast.com/weekly-pick/ko_20200728
- https://ui.toast.com/weekly-pick/ko_20210526
