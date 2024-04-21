<template>
  <div class="mapview">
      <div id="map"></div>
      <div class="button-group">
        <button @click="changeSize(0)">Hide</button>
        <button @click="changeSize(500)">show</button>
        <button @click="displayMarker(markerPositions1)">marker set 1</button>
        <button @click="displayMarker(markerPositions2)">marker set 2</button>
        <button @click="displayMarker([])">marker set 3 (empty)</button>
        <button @click="displayInfoWindow">infowindow</button>
      </div>
   
   
  </div>
</template>

<script>
import { toRaw } from "vue";
export default {
  name: "KakaoMap",
  data() {
    return {
      markerPositions1: [
        [33.452278, 126.567803],
        [33.452671, 126.574792],
        [33.451744, 126.572441],
      ],
      markerPositions2: [
        [37.499590490909185, 127.0263723554437],
        [37.499427948430814, 127.02794423197847],
        [37.498553760499505, 127.02882598822454],
        [37.497625593121384, 127.02935713582038],
        [37.49629291770947, 127.02587362608637],
        [37.49754540521486, 127.02546694890695],
        [37.49646391248451, 127.02675574250912],
      ],
      markers: [],
      infowindow: null,
    };
  },
  mounted() {
    if (window.kakao && window.kakao.maps) {
      this.initMap();
    } else {
      const script = document.createElement("script");
      /* global kakao */
      script.onload = () => kakao.maps.load(this.initMap);
      script.src =
        `//dapi.kakao.com/v2/maps/sdk.js?autoload=false&appkey=${process.env.VUE_APP_KAKAO_API_KEY}`;
      document.head.appendChild(script);
    }
  },
  methods: {
    initMap() {
      const container = document.getElementById("map");
      const options = {
        center: new kakao.maps.LatLng(33.450701, 126.570667),
        level: 5,
      };

      //지도 객체 등록
      this.map = new kakao.maps.Map(container, options);
    },
    changeSize(size) {
      const container = document.getElementById("map");
      container.style.width = `${size}px`;
      container.style.height = `${size}px`;
      toRaw(this.map).relayout();
    },
    displayMarker(markerPositions) {
      if (this.markers.length > 0) {
        this.markers.forEach((marker) => marker.setMap(null));
      }

      const positions = markerPositions.map(
        (position) => new kakao.maps.LatLng(...position)
      );

      if (positions.length > 0) {
        this.markers = positions.map(
          (position) =>
            new kakao.maps.Marker({
              map: toRaw(this.map),
              position,
            })
        );

        const bounds = positions.reduce(
          (bounds, latlng) => bounds.extend(latlng),
          new kakao.maps.LatLngBounds()
        );

        toRaw(this.map).setBounds(bounds);
      }
    },
    displayInfoWindow() {
      if (this.infowindow && this.infowindow.getMap()) {
        //이미 생성한 infowindow가 있기 때문에 지도 중심좌표를 infowindow 좌표로 이동시킴
        toRaw(this.map).setCenter(this.infowindow.getPosition());
        return;
      }

      var iwContent = '<div style="padding:5px;">Hello World!</div>', // infowindow에 표출될 내용으로 HTML 문자열이나 document element가 가능함
        iwPosition = new kakao.maps.LatLng(33.450701, 126.570667), // infowindow 표시 위치
        iwRemoveable = true; // removeable 속성을 true로 설정하면 infowindow를 닫을 수 있는 x버튼이 표시됨

      this.infowindow = new kakao.maps.InfoWindow({
        map: toRaw(this.map), // infowindow가 표시될 지도
        position: iwPosition,
        content: iwContent,
        removable: iwRemoveable,
      });

      toRaw(this.map).setCenter(iwPosition);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.mapview {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}


#map {
  display: flex;
  width: 500px;
  height: 500px;

}

.button-group {
  margin: 10px 0px;
}

button {
  margin: 0 3px;
}
</style>
