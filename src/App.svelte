<script lang="ts">
	type Currency = {
		code: string;
		name: string;
		symbol: string;
	};

	type Language = {
		name: string;
	};

	type Country = {
		name: string;
		alpha2Code: string;
		alpha3Code: string;
		capital: string;
		population: number;
		languages: Language[];
		currencies: Currency[];
		flag: string;
	};

	const url = "https://restcountries.eu/rest/v2/all?fields=name;capital;currencies;languages;flag;population;alpha3Code;alpha2Code";

	const getCounries = async (): Promise<Country[]> => {
		const response = await fetch(url);
		const result = (await response.json()) as Country[];
		return result;
	};

	export let name: string;
</script>

<main>
	<h1>{name} Countries</h1>
	{#await getCounries()}
		<p>...waiting</p>
	{:then result}
	{console.log(result)}
		<div>{JSON.stringify(result, null, 2)}</div>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
