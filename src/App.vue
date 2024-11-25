<template>
	<div class="app-container">
		<h1>Vehicle Selector</h1>

		<p v-if="errorMessage" class="error">{{ errorMessage }}</p>

		<div class="dropdowns-container">
			<!-- Year -->
			<div class="dropdown-container">
				<select @change="fetchMakes" v-model="selectedYear">
					<option value="" disabled selected hidden>
						Select Year
					</option>
					<option
						v-for="(year, index) in years"
						:key="index"
						:value="year"
					>
						{{ year }}
					</option>
				</select>
			</div>

			<!-- Make -->
			<div class="dropdown-container">
				<select
					:disabled="!selectedYear"
					@change="fetchModels"
					v-model="selectedMake"
				>
					<option value="" disabled selected hidden>
						Select Make
					</option>
					<option
						v-for="(make, index) in makes"
						:key="index"
						:value="make"
					>
						{{ make }}
					</option>
				</select>
			</div>

			<!-- Model -->
			<div class="dropdown-container">
				<select :disabled="!selectedMake" v-model="selectedModel">
					<option value="" disabled selected hidden>
						Select Model
					</option>
					<option
						v-for="(model, index) in models"
						:key="index"
						:value="model"
					>
						{{ model }}
					</option>
				</select>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			years: [],
			makes: [],
			models: [],
			selectedYear: null,
			selectedMake: null,
			selectedModel: null,
			errorMessage: "",
		};
	},
	methods: {
		fetchYears() {
			fetch(
				"https://new.api.nexusautotransport.com/api/vehicles/years",
				{
					headers: {
						Accept: "application/json",
						"Content-Type": "application/json",
					},
				}
			)
				.then((response) => response.json())
				.then((data) => {
					this.years = data.data || [];
				})
				.catch(() => {
					this.errorMessage =
						"Unable to load years. Please try again later.";
				});
		},

		fetchMakes() {
			if (!this.selectedYear) return;

			this.makes = [];
			this.models = [];
			this.selectedMake = null;
			this.selectedModel = null;
			this.errorMessage = "";

			fetch(
				`https://new.api.nexusautotransport.com/api/vehicles/makes?year=${this.selectedYear}`,
				{
					headers: {
						Accept: "application/json",
						"Content-Type": "application/json",
					},
				}
			)
				.then((response) => response.json())
				.then((data) => {
					this.makes = data.data
						? data.data.map((make) => make.name)
						: [];
					if (this.makes.length === 0) {
						this.errorMessage =
							"No makes available for the selected year.";
					}
				})
				.catch(() => {
					this.errorMessage =
						"Unable to load makes. Please try again later.";
				});
		},

		fetchModels() {
			if (!this.selectedYear || !this.selectedMake) return;

			this.models = [];
			this.selectedModel = null;
			this.errorMessage = "";

			fetch(
				`https://new.api.nexusautotransport.com/api/vehicles/models?year=${this.selectedYear}&make=${this.selectedMake}`,
				{
					headers: {
						Accept: "application/json",
						"Content-Type": "application/json",
					},
				}
			)
				.then((response) => response.json())
				.then((data) => {
					this.models = data.data
						? data.data.map((model) => model.model)
						: [];
					if (this.models.length === 0) {
						this.errorMessage =
							"No models available for the selected make and year.";
					}
				})
				.catch(() => {
					this.errorMessage =
						"Unable to load models. Please try again later.";
				});
		},
	},
	mounted() {
		this.fetchYears();
	},
};
</script>

<style>
.app-container {
	font-family: Arial, sans-serif;
	max-width: 600px;
	margin: 40px auto;
	text-align: center;
	background-color: #f9f9f9;
	border: 1px solid #ddd;
	border-radius: 10px;
	padding: 20px;
	box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h1 {
	font-size: 24px;
	color: #333;
}

.error {
	color: #ff4d4d;
	font-weight: bold;
	margin: 10px 0;
}

.dropdowns-container {
	display: flex;
	flex-direction: column;
	gap: 15px;
	margin-top: 20px;
}

.dropdown-container {
	display: flex;
	flex-direction: column;
	gap: 10px;
}

select {
	padding: 5px;
	font-size: 18px;
}
</style>
