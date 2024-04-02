---
title: "Google Maps Platform의 엄청난 가격 상승"
slug: "/google-maps-skyrocketing-price"
date: "2018-06-08"
---

지난 5월2일 [Google은 Google Maps Platform](https://mapsplatform.googleblog.com/2018/05/introducing-google-maps-platform.html)을 발표하였다. 그냥 그러려니 했다가 뒤늦게 살펴보니 기존 Google Maps API 를 사용하던 서비스들에게는 엄청난 폭탄이 될 것 같다.

> 기본 무료 사용 범위가 많이 낮아졌다. 그리고, 1천 건 당 비용이 10배 내외로 상승했다.

그래서, 최대 15배나 상승했다던가 좀 자극적인 표현을 사용한 기사(“[Insane, shocking, outrageous: Developers react to changes in Google Maps API](http://geoawesomeness.com/developers-up-in-arms-over-google-maps-api-insane-price-hike/)”)도 있는데, 새로운 요금 체계를 이해하고 나면 전혀 자극적인 제목이 아니었다고 느끼게 된다.

Google은 기존과 상품(product) 체계에 큰 변화를 주었다. 기존엔 약 18개 정도의 개별적인 API들이 있었는데, Google Maps Platform에서는 Maps, Routes, Places로 크게 3가지 서비스로 나누었다.

## Geocoding API는 9~16배나 상승

내가 사용하는 Google API 중 위경도 좌표를 주면 해당 지점의 주소를 알려주는 GeoCoding API 를 살펴보자. 기존에는 하루 2500건을 초과하면 1000 건당 0.5달러였다. 새로운 요금체계에서는 1000 건당 5달로 한달 요금을 계산한 후, $200달러를 감해준다. 단순히 봐도 1000 건당 비용이 10배 상승한 가격이다.

하루에 3천번 GeoCoding API를 호출한다면, 현재는 월 $15를 지불하면 되지만, 새로운 요금체계에서는 무려 $250를 지불해야 한다. 16.7배 상승한 것이다. 하루 1만번 호출한다면 $120 였던 비용이 무려 $1,100 로 상승한다. 9.2배 상승한 것이다. (아래 표에서 새로운 요금체계는 Google이 제공한 cost calcualtor로 계산한 것임)

![Comparison between the old and new pricing](./img//2018/google-maps-pricing.webp)

$200를 감해주는 것은 각 API마다 감해주는 것이 아니다. 전체 Google Maps Platform 사용료에서 $200를 감해주는 것이다. 따라서, 여러 API를 사용한다면, 체감하는 $200 할인은 생각보다 무척 적을 수 있다.

## 검색 관련 API의 요금

기존 검색 API인 Google Places API Web Service 는 하루 15만건까지 무료였기 때문에 중소 규모 서비스에서는 완전 무료라고 봐도 되었을 것이다.

![기존 Google Places API 서비스의 요금 체계](./img//2018/previous-pricing-google-places-api.webp)

그러나, 새로운 검색 API에서는 1000 건당 $17 이다. 하루 3000 번 검색 API를 호출했을 때 기존에는 무료였지만, 앞으로는 $1330 를 Google에 지불해야 한다. 소규모 서비스는 Google 검색 사용하지 말라는 선고나 다름없어 보인다.

![새로운 검색 서비스 중 하나인 Places Details의 요금 체계](./img//2018/google-places-pricing-detail.webp)

그런데, 이게 다가 아니다. 단순히 API 호출한 것에만 과금하는 것이 아니고, 리턴 받는 데이터에 따라서도 요금이 발생하도록 하였다. 리턴 받는 데이터의 종류는 Basic Data, Contact Data, Atmosphere Data의 3 가지이다. Basic은 추가 과금이 없고, Contact는 1000 건당 $3, Atmosphere는 1000 건당 $5.

그래서, 어떤 데이터를 리턴받을 건인지 살펴보고 요청할 때부터 Basic Data만 달라고 해야 추가 과금이 없게 된다.

## Google Maps 는?

기존 Google Maps for Android/iOS API 는 새로운 서비스에서 Mobile Native Dynamic Maps인 것 같다. 다행히도 이것은 기존과 동일하게 사용량에 관계없이 무료이다. 웹에서 Google Maps를 이용했던 경우는 비용이 많이 상승할 것이다.

![](./img//2018/new-google-maps-pricing-plan.webp)

&nbsp;

## Google 너무하다

Google이 어마 어마한 지도 플랫폼을 그동안 만들어왔고 앞으로도 유지하는데 많은 비용이 들어가기에 요금을 올릴 필요는 있을 수 있다. 인정한다.

그러나, 새로운 요금 체계가 10%, 20%도 아니고 몇배에 해당하는 매우 매우 큰 비용 상승을 가져올 것이라는 것을 전혀 언급하지 않은 점, 그리고 그걸 잘 알기 어렵도록 용어와 체계를 만들어서 사용자가 직접 많은 수고를 해야만 하게 한 점, 이런 것은 사용자로써 정말 황당하다.

## 대안은?

https://mapfit.com/ 와 https://www.openstreetmap.org/ 가 Google Maps의 대안이 되지 않을까. Bing 지도를 언급한 댓글도 볼 수 있었다.
미세빅은 React Native 에 React Native Maps를 사용하고 있는데, React Native Maps가 Google Maps만 지원하고 있어서(iOS에서는 Apple Map도 가능) 기본적으로 지도는 Google Maps를 사용해야 한다. 그러나, 검색과 Geocoding은 Google을 다른 서비스를 사용하도록 해야겠다.

## References

- [기존 Google Maps API 요금 체계](https://developers.google.com/maps/pricing-and-plans/?hl=ko)
- [새로운 Google Maps Platform의 요금 체계](https://cloud.google.com/maps-platform/pricing/sheet/)
- 새로운 Google Maps Platform에 대한 설명 “[Understanding Billing for Maps, Routes, and Places](<https://developers.google.com/maps/billing/understanding-cost-of-use?__utma=231840010.622921098.1509770717.1528445545.1528445545.1&__utmb=231840010.0.10.1528445545&__utmc=231840010&__utmx=-&__utmz=231840010.1528445545.1.1.utmcsr=(direct)|utmccn=(direct)|utmcmd=(none)&__utmv=-&__utmk=28162303>)”

> 이 글은 2018.6.8, Medium에 썼던 글을 옮긴 것입니다
