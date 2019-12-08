---
layout: post
title: "Marker Clusterer"
description: ""
category: "nurserymap"
tags: [nurserymap]
---

화면상에 `Marker`가 너무 빽빽하게 배치되어 있으면, 정보로서 가치도 없고 보기도 좋지 않습니다.
![alt text]({{ site.url }}/assets/images/too_many_markers.png)

빽빽한 `Marker`를 적당히 보기 좋게 표시해주는 [Marker Clusterer](http://google-maps-utility-library-v3.googlecode.com/svn/trunk/markerclusterer/docs/examples.html)라는 라이브러리가 있습니다.

`Google Map` 스크립트 이전에 `Marker Clusterer` 스크립트를 include해주고,

``` html
<script type="text/javascript" src="http://google-maps-utility-library-v3.googlecode.com/svn/trunk/markerclusterer/src/markerclusterer.js"></script>
<script type="text/javascript"
      src="http://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&sensor=true">
</script>
```

초기화 코드에서 `Marker Clusterer` 객체 초기화

``` javascript
var mcOptions = {gridSize: 70, maxZoom: 14};
mc = new MarkerClusterer(map, [], mcOptions);
```

`Marker` 객체 생성할때마다 mc에 등록해주면 됩니다.

``` javascript
marker[index] = new google.maps.Marker({ ... });
mc.addMarker(marker[index]);
```

### Marker Clusterer 적용
![alt text]({{ site.url }}/assets/images/marker_clusterer.png)

{% include JB/setup %}
