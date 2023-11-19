<template>
	<div class="flex flex-col flex-1 items-center">
		<!-- Banner -->
		<div
			v-if="route.query.preview"
			class="text-white p-4 bg-weather-secondary w-full text-center"
		>
			<p>
				You are currently previewing this city, click the "+" icon to add this
				city to your list.
			</p>
		</div>
		<!-- Weather Overview -->
		<div class="flex flex-col items-center text-white py-4">
			<h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
			<p class="text-sm">
				{{
					new Date().toLocaleDateString('sv-SE', {
						weekday: 'long',
						day: '2-digit',
						month: 'short',
					})
				}}
				{{
					new Date(weatherData.dt * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
			<p class="text-8xl">{{ Math.round(weatherData.main.temp) }}&deg;c</p>
			<img
				class="w-[150px] h-auto"
				:src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
				:alt="`${weatherData.weather[0].description}`"
				:title="`${weatherData.weather[0].description}`"
			/>
			<p class="text-xl">
				Känns som
				{{ Math.round(weatherData.main.feels_like) }}&deg;c
			</p>
			<p>
				L {{ Math.round(weatherData.main.temp_min) }}&deg;c / H
				{{ Math.round(weatherData.main.temp_max) }}&deg;c
			</p>
			<p>
				<i class="fa-solid fa-wind"></i> {{ weatherData.wind.speed }} m/s
				<i
					:style="{ transform: `rotate(${weatherData.wind.deg}deg)` }"
					class="fa-solid fa-angles-down"
				></i>
			</p>
			<p>
				<i class="fa-regular fa-sun"></i>
				{{
					new Date(weatherData.sys.sunrise * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
			<p>
				<i class="fa-regular fa-moon"></i>
				{{
					new Date(weatherData.sys.sunset * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
		</div>
	</div>
	<hr class="border-white border-opacity-10 border w-full" />
	<!-- Hourly Weather -->
	<div class="max-w-screen-md w-full py-12">
		<div class="mx-8 text-white">
			<h2 class="mb-4">Kommande 24h</h2>
			<div class="flex gap-10 overflow-x-scroll">
				<div
					v-for="hourData in forecastData.list.slice(0, 9)"
					:key="hourData.dt"
					class="flex flex-col gap-4 items-center"
				>
					<p class="whitespace-nowrap text-md">
						{{
							new Date(hourData.dt_txt).toLocaleTimeString('sv-SE', {
								weekday: 'long',
								hour: 'numeric',
								minute: 'numeric',
							})
						}}
					</p>
					<img
						class="w-auto h-[50px] object-cover"
						:src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
						alt=""
					/>
					<p class="text-xl">{{ Math.round(hourData.main.temp) }}&deg;c</p>
					<p class="text-sm text-center">
						{{ Math.round(hourData.main.feels_like) }}&deg;c
					</p>
				</div>
			</div>
		</div>
	</div>

	<hr class="border-white border-opacity-10 border w-full" />
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

const route = useRoute()
const API_KEY = import.meta.env.VITE_OPEN_WEATHER_KEY
const BASE_URL = 'https://api.openweathermap.org/data/2.5/'

const getWeatherData = async (param) => {
	try {
		const weatherData = await axios.get(
			`${BASE_URL}${param}?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}&units=metric`
		)
		return weatherData.data
	} catch (err) {
		console.error('ERROR::', err)
	}
}

const weatherData = await getWeatherData('weather')
const forecastData = await getWeatherData('forecast')
</script>