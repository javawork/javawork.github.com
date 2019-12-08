---
layout: post
title: "Google Maps API"
description: ""
category: "nurserymap"
tags: [nurserymap]
---

특정 지역의 어린이집 정보를 `JSON`형태로 가져옵니다.

``` javascript
$.getJSON(‘data/query’,{'area1':area1, 'area2':area2},function(data) {
...
}
```

아래와 같은 형태의 `JSON`데이터가 리턴됩니다.

``` json
[
	{
		"own": "직장", 
		"capacity": "35명", 
		"title": "종로구청직장어린이집", 
		"auth": "인증시설", 
		"phone": "02-720-5852", 
		"address": "서울특별시 종로구 삼봉로 43", 
		"lat": 126.97912289999999, 
		"lng": 37.572832839999997, 
		"id": "11110000039"
	},
	{
		"own": "국공립",
		...
	}
]
```

위도/경도 데이터로 `Marker`를 생성합니다.

``` javascript
marker[index] = new google.maps.Marker({
	position: new google.maps.LatLng(lng, lat),
	map: map,
	title: data['title']
});
```

`Marker`를 클릭하면 보여줄 `InfoWindow`를 만들고, 등록해 줍니다.

``` javascript
function build_text_for_infowindow( entry ) {
  return '<table>'
  	+ '<tr><td>' + data['title'] + '</td></tr>'
    + '<tr><td>' + data['address'] + '</td></tr>'
    + '<tr><td>'
    + '정원 : ' + data['capacity']
    + '</td></tr>'
    + '</table>';  
}

info_text = build_text_for_infowindow(data);
infowindow[index] = new google.maps.InfoWindow({
	content: info_text
});
google.maps.event.addListener(marker[index], 'click', function() {
	infowindow[this.index].open(map, marker[this.index]);
});
```
### Marker and InfoWindow
![alt text]({{ site.url }}/assets/images/show_marker.png)

{% include JB/setup %}
