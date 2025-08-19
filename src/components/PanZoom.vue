<script setup>
import {onMounted, useTemplateRef, watch, ref} from "vue";
const props = defineProps({
  src: {
    type: String,
    required: true
  },
  maxScale: {
    type: Number,
    default: 5
  },
  minScale: {
    type: Number,
    default: 0.5
  },
  bounds: {
    type: Object,
    default: () => {
      return {top: 0, right: 350, bottom: 300, left: 0}
    }
  },
  enabledDoubleClickZoom: {
    type: Boolean,
    default: true
  },
  smoothScroll: {
    type: Boolean,
    default: true
  },
  zoomDoubleClickSpeed: {
    type: Number,
    default: 1.2
  },
  zoomedRebound: { // 缩放结束后，如果scale小于1，则回弹到1
    type: Boolean,
    default: true
  }
});
const emits = defineEmits(['panstart', 'panend', 'pan', 'zoom', 'zoomend', 'transform']);
const imageRef = useTemplateRef("imageRef");
let instance = null;
const error = ref("");
function init() {
  // 需要等待图片加载完成，否则初始时图片无法居中
  imageRef.value.onload = () => {
    try {
      instance = window.panzoom(imageRef.value, {
        maxZoom: props.maxScale,
        minZoom: props.minScale,
        bounds: props.bounds,
        boundsPadding: 1,
        autocenter: true,
        smoothScroll: props.smoothScroll,
        zoomDoubleClickSpeed: props.zoomDoubleClickSpeed,
        enableDoubleClick: props.enabledDoubleClickZoom,
      });
      imageRef.value.style.opacity = 1;
      bind();
    } catch (e) {
      error.value = e;
    }
  }
}

function bind() {
  instance.on('panstart', function(e) {
    emits('panstart', e);
  });

  instance.on('pan', function(e) {
    emits('panend', e);
  });

  instance.on('panend', function(e) {
    emits('panend', e);
  });

  instance.on('zoom', function(e) {
    // emits('zoom', scale);
  });

  instance.on('zoomend', function(e) {
    const {scale} = instance.getTransform();
    if (scale < 1 && props.zoomedRebound) {
      instance.smoothZoomAbs(0, 0, 1);
      setTimeout(() =>{
        emits('zoom', 1);
      }, 100);
    } else {
      emits('zoom', scale);
    }
  });

  // 双击使图片缩小到1
  instance.on('zoomMin', function(e) {
    emits('zoom', 1);
  });
  // 双击使图片放大到中间值
  instance.on('zoomMean', function(e) {
    emits('zoom', Math.round((props.maxScale + props.minScale) / 2));
  });

  instance.on('transform', function(e) {
    emits('transform', e);
  });
}

onMounted(() => {
  init();
});

</script>

<template>
  <div class="map-container">
    <div style="color: white">{{error}}</div>
    <img ref="imageRef" style="display: block; opacity: 0" :src="src" alt="img">
  </div>
</template>

<style scoped>
.map-container {
  width: 350px;
  height: 300px;
  border: 1px solid red;
  overflow: hidden;

  img {
    will-change: transform;
    width: 100%;
  }
}
</style>