<template>
  <div class="weather" v-if="weatherData.adCode.city && weatherData.weather.weather">
    <span>{{ weatherData.adCode.city }}&nbsp;</span>
    <span>{{ weatherData.weather.weather }}&nbsp;</span>
    <span>{{ weatherData.weather.temperature }}℃</span>
    <span class="sm-hidden">
      &nbsp;{{
        weatherData.weather.winddirection?.endsWith("风")
          ? weatherData.weather.winddirection
          : weatherData.weather.winddirection + "风"
      }}&nbsp;
    </span>
    <span class="sm-hidden">{{ weatherData.weather.windpower }}</span>
  </div>
  <div class="weather" v-else>
    <span>天气数据获取失败</span>
  </div>
</template>

<script setup>
import { reactive, onMounted, h } from "vue";
import { Error } from "@icon-park/vue-next";

// 天气数据
const weatherData = reactive({
  adCode: {
    city: null, // 城市
    adcode: null, // 城市编码
  },
  weather: {
    weather: null, // 天气现象
    temperature: null, // 实时气温
    winddirection: null, // 风向描述
    windpower: null, // 风力级别
  },
});

// 获取天气数据
const getWeatherData = async () => {
  try {
    const response = await fetch("https://uapis.cn/api/v1/misc/weather");

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();

    // 映射 API 的数据结构
    weatherData.adCode = {
      // 如果想显示区级，可以将 data.city 改为 data.district
      city: data.city || "未知地区",
      adcode: data.adcode,
    };

    weatherData.weather = {
      weather: data.weather,
      temperature: data.temperature,
      winddirection: data.wind_direction,
      windpower: data.wind_power,
    };
  } catch (error) {
    console.error("天气信息获取失败:", error);
    onError("天气信息获取失败");
  }
};

// 报错信息
const onError = (message) => {
  ElMessage({
    message,
    icon: h(Error, {
      theme: "filled",
      fill: "#efefef",
    }),
  });
};

onMounted(() => {
  // 调用获取天气
  getWeatherData();
});
</script>
