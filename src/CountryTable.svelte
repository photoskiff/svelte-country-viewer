<script lang="ts">
    import FilterBox from "./FilterBox.svelte";
    import type { Country, Currency } from "./model/country";
    import { getContext } from 'svelte';
    import CountryDetail from "./CountryDetail.svelte"
    type Order = "asc" | "desc" | undefined;

    export let countries: Country[] = [];

    let filtered: Country[] = [];

    const numberWithCommas = (x: number) =>
        x && x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    const ccyDisplay = (code: string) => (code === "(none)" ? "" : code ?? "");
    const curr = (currencies: Currency[]) =>
        currencies.reduce<string>(
            (acc, ccy) =>
                acc === ""
                    ? ccyDisplay(ccy?.code)
                    : `${acc}, ${ccyDisplay(ccy?.code)}`,
            ""
        );
    const { open } = getContext('simple-modal');
    const showPopup = country => {
		open(CountryDetail, { country });
	};
</script>

<div>
    <FilterBox {countries} bind:filtered />
    <table>
        <tr>
            <th /><th>Country</th><th>Population</th><th>Alpha2</th><th
                >Alpha3</th
            ><th>Capital</th><th>Currency</th>
        </tr>
        {#each filtered as { name, population, alpha2Code, alpha3Code, flag, capital, currencies, languages }}
            <tr>
                <td on:click={() => showPopup({name, currencies, languages, capital, flag})}>
                    <img width="14" height="14" alt="flag" src={flag} style="cursor:pointer" /></td>
                <td>{name}</td>
                <td class="population">{numberWithCommas(population)}</td>
                <td>{alpha2Code}</td>
                <td>{alpha3Code}</td>
                <td>{capital}</td>
                <td class="ccy">{curr(currencies)}</td>
            </tr>
        {/each}
    </table>
</div>

<style>
    table {
        font-family: arial, sans-serif;
        border-collapse: collapse;
        margin-left: auto;
        margin-right: auto;
    }

    td,
    th {
        border: 1px solid #dddddd;
        text-align: left;
        padding: 8px;
    }

    td {
        font-size: 12px;
    }

    .ccy {
        width: 40px;
    }

    .population {
        text-align: right;
    }

    tr:nth-child(even) {
        /* background-color:rgb(228, 228, 235); */
        background-color: rgb(228, 240, 248);
    }
</style>
