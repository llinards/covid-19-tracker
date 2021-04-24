<template>
	<main v-if="!loading">
		<DataTitle :text="title" :dataDate="dataDate" />
		<DataBoxes :stats="stats" />
		<CountrySelect :countries="countries" @get-country="getCountryData" />
		<button
			@click="clearCountryData()"
			v-if="stats.Country"
			class="bg-green-700 text-white rounded p-3 mt-10 focus:outline-none hover:bg-green-600"
		>
			Clear Country
		</button>
	</main>
	<main class="flex flex-col align-center justify-center text-center" v-else>
		<div class="text-gray-500 text-3xl mt-10 mb-6">
			Fetching Data
		</div>
		<img :src="loadingImage" class="w-24 m-auto" alt="" />
	</main>
</template>

<script>
import axios from "axios";
import DataTitle from "../components/DataTitle";
import DataBoxes from "../components/DataBoxes";
import CountrySelect from "../components/CountrySelect";
export default {
	name: "Home",
	components: {
		DataTitle,
		DataBoxes,
		CountrySelect,
	},
	data() {
		return {
			loading: true,
			title: "Global",
			dataDate: "",
			stats: {},
			countries: [],
			loadingImage: require("../assets/loading.gif"),
		};
	},
	methods: {
		async fetchCovidData() {
			try {
				const data = axios.get("https://api.covid19api.com/summary");
				return data;
			} catch (error) {
				console.log(error);
			}
		},
		getCountryData(country) {
			this.stats = country;
			this.title = country.Country;
		},
		async clearCountryData() {
			this.loading = true;
			const data = await this.fetchCovidData();
			this.title = "Global";
			this.stats = data.data.Global;
			this.loading = false;
		},
	},
	async created() {
		const data = await this.fetchCovidData();
		this.dataDate = data.data.Date;
		this.stats = data.data.Global;
		this.countries = data.data.Countries;
		this.loading = false;
	},
};
</script>
