<template>
    <div class="kakaomap">
        <KakaoMap />
    </div>
</template>
  
<script>
import KakaoMap from "@/components/KakaoMap.vue";

export default {
    name: "KakaoMapView",
    components: {
        KakaoMap,
    },
};
</script>