<template>
  <div id="loader-wrapper" :class="store.imgLoadStatus ? 'loaded' : null">
    <div class="loader">
      <img class="loader-image" src="/images/loading.gif" alt="加载动画" />
      <div class="loader-text">
        <span class="name">
          {{ siteName }}
        </span>
        <span class="tip"> 加载中{{ loadingMarks }} </span>
      </div>
    </div>
    <div class="loader-section section-left" />
    <div class="loader-section section-right" />
  </div>
</template>

<script setup>
import { mainStore } from "@/store";

const store = mainStore();

// 配置
const siteName = import.meta.env.VITE_SITE_NAME;
const loadingMarks = ref("!");
let loadingMarkTimer = null;

// 使用感叹号循环表示加载进度
const updateLoadingMarks = () => {
  loadingMarks.value = loadingMarks.value.length >= 3 ? "!" : `${loadingMarks.value}!`;
};

const stopLoadingMarks = () => {
  if (loadingMarkTimer) {
    clearInterval(loadingMarkTimer);
    loadingMarkTimer = null;
  }
};

onMounted(() => {
  loadingMarkTimer = setInterval(updateLoadingMarks, 500);
});

watch(
  () => store.imgLoadStatus,
  (loaded) => {
    if (loaded) stopLoadingMarks();
  },
);

onBeforeUnmount(stopLoadingMarks);
</script>

<style lang="scss" scoped>
#loader-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 999;
  overflow: hidden;
  .loader {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    .loader-image {
      width: min(80vw, 480px);
      height: auto;
      max-height: 50vh;
      object-fit: contain;
      z-index: 2;
    }
    .loader-text {
      display: flex;
      flex-direction: column;
      align-items: center;
      color: #fff;
      z-index: 2;
      margin-top: 40px;
      font-size: 24px;
      .tip {
        margin-top: 6px;
        font-size: 18px;
        opacity: 0.6;
      }
    }
  }
  .loader-section {
    position: fixed;
    top: 0;
    width: 51%;
    height: 100%;
    background: #333;
    z-index: 1;
    &.section-left {
      left: 0;
    }
    &.section-right {
      right: 0;
    }
  }
  &.loaded {
    visibility: hidden;
    transform: translateY(-100%);
    transition:
      transform 0.3s 1s ease-out,
      visibility 0.3s 1s ease-out;
    .loader {
      .loader-image,
      .loader-text {
        opacity: 0;
        transition: opacity 0.3s ease-out;
      }
    }
    .loader-section {
      &.section-left {
        transform: translateX(-100%);
        transition: transform 0.5s 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
      }
      &.section-right {
        transform: translateX(100%);
        transition: transform 0.5s 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
      }
    }
  }
}

</style>
