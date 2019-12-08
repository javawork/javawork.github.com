---
layout: post
title: "Collect Data"
description: ""
category: "nurserymap"
tags: [nurserymap]
---

어린이집 데이터는 보건복지부 [아이사랑 교육포털](http://www.childcare.go.kr/)에서 검색이 가능합니다.

### 목록보기
![alt text]({{ site.url }}/assets/images/search_nursery.png)
### 상세정보
![alt text]({{ site.url }}/assets/images/nursery_detail.png)

`크롬` 브라우져의 개발자 도구를 이용하면 어떻게 요청을 보내야 응답을 받을 수 있을지 알 수 있습니다.
![alt text]({{ site.url }}/assets/images/chrome_devmode.png)

`Google App Engine`에서는 다른 웹페이지에 요청을 보내는 방법으로 `urlfetch`라는 모듈을 제공합니다. 코드로 예를 들면 아래와 같습니다.

``` python
result = urlfetch.fetch(
	url= "http://www.childcare.go.kr/.../NurserySIPL.jsp?ctprvn=%s&signgu=%s&offset=%d"%(ctprvn, signgu, offset),
	method=urlfetch.GET,
	headers={'Content-Type': 'application/x-www-form-urlencoded'},
	deadline=60
)
parse( result.content )
```

이 html을 분석하면 주소, 연락처, 인증유형, 총원 등 여러가지 정보를 얻을 수 있습니다. 이 데이터를 ndb에 넣습니다.

``` python
model = NurseryModel(
		parent=ndb.Key('nursery', '*notitle*'),
		id = id,
		title = title,
		address = address,
		lng = lng,
		lat = lat
		... )
model.put()
```

전국 어린이집 데이터를 수집하기 위해서는 지역코드를 모두 파악해서 그것을 인자로 `urlfetch`로 데이터 가져오기 -> 데이터 분석 -> `ndb` insert를 반복해야합니다. `App Engine`의 `cron job`은 시간과 url을 설정해 놓으면, 서버에서 url을 일정 간격으로 호출해줍니다. `cron.yaml`로 설정이 가능합니다.

``` yaml
cron
- description: collect list
  url: /collect/list
  schedule: every 5 minutes
  timezone: Asia/Seoul
```

[아이사랑 교육포털](http://www.childcare.go.kr/)에 의하면 전국에 4만1천개 정도의 어린이집이 있습니다. 데이터를 수집했으니 이제 `Google Map`에 보여주면 됩니다.

{% include JB/setup %}
