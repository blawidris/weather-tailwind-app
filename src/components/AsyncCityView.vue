<template lang="">
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div class="text-white p-4 bg-weather-secondary w-full text-center" v-if="route.query.preview">
            <p>You are current previewing city click the "+" icon to start tracking this city.</p>
        </div>

        <!--  Weather Overview-->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-8xl mb-2">
                {{route.params.state}}
            </h1>
            <p class="text-sm mb-12">

                {{
                    new Date(weatherData.currentTime).toLocaleDateString('en-US', {
                        weekday: 'short',
                        day: '2-digit',
                        month:'long'
                    })
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString('en-US', {
                        timeStyle: 'short'
                    })
                }}
            </p>

            <p class="text-8xl mb-8">
                {{Math.round(weatherData.currentConditions.temp)}}&deg

            </p>

            <!-- <div class="text-center"> -->
                <p>
                    Feels like {{weatherData.currentConditions.feelslike}}
                </p>
                <p class="pb-3 capitalize">
                    {{weatherData.description}}
                </p>
                 <img class="w-[150px] h-auto" :src="require(`@/assets/img/${weatherData.currentConditions.icon}.png`)" height="24">
            <!-- </div> -->
        </div>

        <hr class="border-opacity-10 border-white border w-full">

        <!-- Hourly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="text-2xl font-medium mb-4">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.days" :key="hourData.datetimeEpoch" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(hourData.currentTime).toLocaleTimeString('en-US', {hours:'numeric'})
                            }}
                        </p>
                        <img class="w-[50px] h-auto object-cover" :src="require(`@/assets/img/${hourData.icon}.png`)" height="24">
                        <p class="text-xl">
                            {{Math.round(hourData.temp)}}&deg
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <hr class="border-opacity-10 border-white border w-full">

        <!-- 7 days forecast -->
<div class="max-w-screen-md w-full py-12">
    <div class="mx-8 text-white">
        <h2 class="text-2xl font-medium mb-4">7 days forecast</h2>
        <div v-for="hourData in weatherData.days" :key="hourData.datetimeEpoch" class="flex items-center">
                        <p class="flex-1 ">
                            {{
                                new Date(hourData.datetimeEpoch * 1000).toLocaleTimeString('en-US', {weekday:'long'})
                            }}
                        </p>
                        <img class="w-[50px] h-auto object-cover" :src="require(`@/assets/img/${hourData.icon}.png`)">
                        <div class="flex gap-2 flex-1 justify-end">
                            <p>H: {{Math.round(hourData.tempmax)}}</p>
                            <p>L: {{Math.round(hourData.tempmin)}}</p>
                        </div>
                        <!-- <p class="text-xl">
                            {{Math.round(hourData.temp)}}&deg
                        </p> -->
                    </div>
    </div>
</div>
    </div>
</template>


<script setup>

import axios from 'axios'
import { useRoute } from 'vue-router';


const route = useRoute();

const apiKey = 'JMXQC4QJ69HZB4Z9HVGM72X2Y';

async function getWeatherData() {
    try {
        const weatherData = await axios.get(`https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${route.query.lng},${route.query.lat}?key=${apiKey}`);

        // calculate current date & time
        const localOffset = new Date().getTimezoneOffset() * 6000;
        const utc = weatherData.data.currentConditions.datetimeEpoch * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.tzoffset;

        //  calculate hourly weather offset
        weatherData.data.days.forEach(day => {
            const utc = day.datetimeEpoch * 1000 + localOffset;
            day.currentTime = utc + 1000 * weatherData.data.tzoffset;
        });

        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
}

const weatherData = await getWeatherData();
console.log(weatherData);
</script>
