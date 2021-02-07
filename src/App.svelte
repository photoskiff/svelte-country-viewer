<script lang="ts">
	import type { Country } from "./model/country";
	import CountryTable from "./CountryTable.svelte";
	import Modal from "./Modal.svelte";
	// import {getCounries} from "./service/countryService";

	const url =
		"https://restcountries.eu/rest/v2/all?fields=name;capital;currencies;languages;flag;population;alpha3Code;alpha2Code";

	const getCounries2 = async (): Promise<Country[]> => {
		const response = await fetch(url);
		const result = (await response.json()) as Country[];
		return result;
	};

	export let name: string;
</script>

<main>
	<h1>{name}</h1>
	{#await getCounries2()}
		<p>...waiting</p>
	{:then result}
	<Modal>
		<CountryTable countries={result} />
	</Modal>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
</main>

<style>
	main {
		text-align: center;
		/* padding: 1em; */
		/* max-width:fit-content; */
		margin: auto;
		margin-top: 0px;
		/* padding-left:40px;
		padding-right: 40px; */
		background-color: ghostwhite;

	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
		margin-block-start: 0px;
		margin-block-end: 10px;
	}

	/* @media (min-width: 640px) {
		main {
			max-width: none;
		}
	} */
</style>
