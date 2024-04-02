---
title: "React Native Maps의 Custom Callout 일부가 잘리는 이슈"
slug: "/react-native-maps-custom-callout-partially-cut-off"
date: "2018-05-27"
---

Google Map을 사용하는 React Native Maps에서 어느 지점을 길게 누르면 해당 지점의 주소를 아래와 같이 보여주려고 한다. 주소의 길이에 맞춰서 Callout의 너비가 자동으로 조정되면서 중국이나 일본의 긴 주소도 한 줄로 보여주어야 한다.

<div style="max-width:480px;">
  <img src="./img//2018/custom-callout-example1.png">
</div>

해외의 긴 주소일 때 Custom Callout의 폭이 넓어진다

<div style="max-width:480px;">
    <img src="./img//2018/custom-callout-example2.webp">
</div>

## 이슈

그런데, 아래와 같이 주소의 오른쪽이 잘리는 현상이 발생한다. Android에서 발생 빈도가 높고 iOS에서는 발생 빈도가 낮다. 이 문제를 해결하는 방법을 알아본다. (현재 개발환경은 Expo v25.0.0, React Native 0.52, React Native Maps v0.19.0 이므로 이 환경에서만 발생하는 것일 수 있음)

<div style="max-width:480px;">
    <img src="./img//2018/custom-callout-clipped-1.png">
</div>

<div style="max-width:480px;">
    <img src="./img//2018/custom-callout-clipped-2.png">
</div>

React Native Maps의 Marker와 Custom Callout을 다음과 같이 하면 모든 경우에 대해서 다 잘 되어야 할 것 같으나 기대한 대로 잘 동작하지 않고 위와 같은 문제가 발생한다. 한참 동안은 Android에서만 발생하는 것 같았으나 iOS에서도 간혹 발생한다.

```jsx
<MapView.Marker
 coordinate={{ latitude, longitude }}
 ...
>
  <MapView.Callout tooltip={true} >
    <View style={styles.calloutView} >
      <Text style={styles.calloutText} >
        {selectedLocationName}
      </Text>
    </View>
  </MapView.Callout>
</MapView.Marker>
```

## 해결 방법

해결 방법은 Callout의 Style prop으로 style={{ alignItems: 'center', width: contentWidth }} 을 추가하는 것이다. contentWidth는 callout의 최대 너비이다.

```jsx{6-6}
<MapView.Marker
 coordinate={{ latitude, longitude }}
 ...
>
  <MapView.Callout tooltip={true}
    style={{ alignItems: 'center', width: contentWidth }}
  >
    <View style={styles.calloutView} >
      <Text style={styles.calloutText} >
        {selectedLocationName}
      </Text>
    </View>
  </MapView.Callout>
</MapView.Marker>
```

그러나, 매우 긴 주소는 2줄이 되어 아래와 같이 원하지 않는 결과가 발생한다.

<div style="max-width:480px;">
    <img src="./img//2018/custom-callout-clipped-3.png">
</div>

이를 해결하기 위해서는 Text의 내용을 1줄로만 보여주고 더 길면 생략하도록 numberOfLines와 ellipsizeMode prop을 추가한다.

```jsx{10-11}
<MapView.Marker
 coordinate={{ latitude, longitude }}
 ...
>
  <MapView.Callout tooltip={true}
    style={{ alignItems: 'center', width: contentWidth }}
  >
    <View style={styles.calloutView} >
      <Text style={styles.calloutText}
            numberOfLines={1}
            ellipsizeMode={'tail'}
      >
        {selectedLocationName}
      </Text>
    </View>
  </MapView.Callout>
</MapView.Marker>
```

그러면, 다음과 같이 긴 주소도 한 줄로 보이면서 넘치는 부분은 … 로 생략한다.

<div style="max-width:480px;">
    <img src="./img//2018/custom-callout-ellipsis.png">
</div>

유사한 이슈가 React Native Maps의 GitHub, "Custom Callouts are cut off after updating module and react-native"에 있고, 여기서 해결 방법으로 제시된 것은 Callout의 style prop에 width와 height를 주거나, position에 'relative'를 주는 것이다. 그러나, 여기서 제시한 방법들로는 본 이슈가 해결되지 않았다. 여러 가지 style 프로터티를 주고 빼고 조합하다가 우연 찾아내었다.

아래와 같은 방법도 시도해보았으나 소용없었다.

```jsx
<Text style={styles.calloutText}
  numberOfLines={1}
  ellipsizeMode={'tail'}
>
```

`style={{ flex: 1, alignItems: 'center' }}` 도 효과가 없다.

## 남은 이슈

Callout 안의 View는 문자열의 길이에 따라 조정되지만, Callout 자체의 width는 contentWidth로 고정되어 있기 때문에 터치 가능한 영역이 보이는 Callout보다 더 넓다. 따라서, 사용자가 Callout의 옆에 있는 지도를 터치하면, 지도가 아닌 Callout 을 터치한 효과가 발생한다. 따라서, 완벽한 해결책은 될 수 없다. ^^;
