<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
        </div>
        <!-- weather overview -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.dt * 1000).toLocaleDateString("en-us", {
                        weekday: "short",
                        day: "2-digit",
                        month: "long"
                    })
                }}
                {{
                    new Date(weatherData.dt * 1000).toLocaleTimeString("en-us", {
                        timeStyle: "short"
                    })
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.main.temp) }}&deg
            </p>
            <p>Feels like {{ Math.round(weatherData.main.feels_like) }}&deg;</p>
            <p class="capitalize">
                {{ weatherData.weather[0].description }}
            </p>
            <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">
        </div>
        <hr class="border-white border-opacity-10 border w-full" />

        <!-- hourly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Hourly Weather</h2>
                <div class="flex gap-8 overflow-x-auto">
                    <template v-for="hourData in weatherHourlyData.list" :key="hourData.dt">
                        <div v-if="hourData.dt > (currentTime - 3600 * 6) && hourData.dt < (currentTime + 3600 * 24)"
                            class="flex flex-col gap-4 items-center">
                            <p class="whitespace-nowrap text-md">
                                {{
                                    new Date((hourData.dt) * 1000).toLocaleTimeString("en-us", { hour: "numeric" })
                                }}
                            </p>
                            <img :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" alt="">
                            <p class="text-xl">
                                {{ Math.round(hourData.main.temp) }}&deg;
                            </p>
                        </div>
                    </template>
                </div>
            </div>
        </div>

        <!-- weekly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 Day forecast</h2>
                <template v-for="day in weatherDailyData" :key="weekday">
                    <div v-if="day.temps.length > 0" class="flex items-center">
                        <p class="flex-1">
                            {{ day.name }}
                        </p>
                        <img :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt=""
                            class="w-[50px] h-[50px] object-cover">
                        <div class="flex flex-1 gap-3 justify-end">
                            <p>H: {{ Math.round(Math.max.apply(Math, day.temps)) }}&deg;</p>
                            <p>L: {{ Math.round(Math.min.apply(Math, day.temps)) }}&deg;</p>
                        </div>
                    </div>
                </template>
            </div>
        </div>

        <!-- remove icon -->
        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
            v-if="!route.query.preview" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const VITE_OPENWEATHER_TOKEN = import.meta.env.VITE_OPENWEATHER_TOKEN
const route = useRoute()
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&lang=vi&units=metric&exclude={part}&appid=${VITE_OPENWEATHER_TOKEN}`)
        return weatherData.data
    } catch (err) {
        console.log(err)
    }
}
const getWeatherHourlyData = async () => {
    try {
        const weatherHourlyData = await axios.get(`https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lon}&lang=vi&units=metric&exclude={part}&appid=${VITE_OPENWEATHER_TOKEN}`)
        return weatherHourlyData.data
    } catch (err) {
        console.log(err)
    }
}


const getWeatherDaily = async () => {
    const dailyData = {}
    await weatherHourlyData.list.map((hourData) => {
        const weekday = new Date(hourData.dt * 1000).toLocaleDateString("en-us", {
            weekday: "short",
        })

        if (dailyData[weekday] !== undefined) {
            if (dailyData[weekday]["list"] !== undefined) {
                dailyData[weekday]["list"].push(hourData)
            } else {
                dailyData[weekday]["list"] = [hourData]
            }
        } else {
            dailyData[weekday] = { name: weekday, weather: hourData.weather }
        }
    })

    for await (const [key, day] of Object.entries(dailyData)) {
        const temps = []
        for (let index in day.list) {
            let item = day.list[index]
            let temp = item.main.temp
            temps.push(temp)
        }
        day["temps"] = temps

    }

    return dailyData
}

const weatherData = await getWeatherData()
const weatherHourlyData = await getWeatherHourlyData()
const currentTime = new Date().getTime() / 1000 + weatherHourlyData.city.timezone
const weatherDailyData = await getWeatherDaily()
await new Promise((res) => setTimeout(res, 300))


const router = useRouter()
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'));
    const updatedCities = cities.filter((city) => city.id !== route.query.id)
    localStorage.setItem("savedCities", JSON.stringify(updatedCities))
    router.push({
        name: "home"
    })
}


</script>