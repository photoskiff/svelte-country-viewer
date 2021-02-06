
<script lang="ts">
	import type { Country, Currency } from "./model/country";
    export let countries : Country[] = [];
    const numberWithCommas = (x: number) => x && x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    const ccyDisplay = (code: string) => code === "(none)" ? "" : code ?? "";
    const curr = (currencies: Currency[]) => currencies.reduce<string>((acc, ccy) => acc === "" ? ccyDisplay(ccy?.code) : `${acc}, ${ccyDisplay(ccy?.code)}`, "")
</script>

<table>
    <tr>
        <th></th><th>Country</th><th>Population</th><th>Alpha2</th><th>Alpha3</th><th>Capital</th><th>Currency</th>
    </tr>
    {#each countries as { name, population, alpha2Code, alpha3Code, flag, capital, currencies }}
        <tr>
            <td><img width=14 height=14 alt="flag" src={flag}/></td>
            <td>{name}</td>
            <td class="population">{numberWithCommas(population)}</td>
            <td>{alpha2Code}</td>
            <td>{alpha3Code}</td>
            <td>{capital}</td>
            <td class="ccy">{curr(currencies)}</td>
        </tr>
    {/each}
</table>

<style>
    table {
    font-family    : arial, sans-serif;
    border-collapse: collapse;
    width          : 60%;
    display:block;
    margin-left: auto;
    margin-right: auto;
}

td,
th {
    border    : 1px solid #dddddd;
    text-align: left;
    padding   : 8px;
}

td{
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