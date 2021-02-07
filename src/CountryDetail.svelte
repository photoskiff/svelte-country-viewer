<script lang="ts">
    import type { Country } from "./model/country";
    export let country: Country;
    const ccyDisplay = (code: string) => (code === "(none)" ? "" : code ?? "");
    const curr = country?.currencies.reduce<string>(
        (acc, ccy) =>
            acc === ""
                ? ccyDisplay(ccy?.code)
                : `${acc}, ${ccyDisplay(ccy?.code)}`,
        ""
    );
    const lang = country?.languages.reduce<string>(
        (acc, lang) => (acc === "" ? lang.name : `${acc}, ${lang.name}`),
        ""
    );
    let { capital, currencies, languages, flag } = country;
</script>

<div class='evenContainer'>
    {#if !country}
        no data!
    {:else}
        <div>
            <h3>Capital City</h3>
            <div>{capital}</div>
            <h3>{currencies.length < 2 ? "Currency" : "Currencies"}</h3>
            <div>{curr}</div>
            <h3>{languages.length < 2 ? "Language" : "Languages"}</h3>
            <div>{lang}</div>
        </div>
        <div>
            <img width={240} height={120} src={flag} alt="flag" />
        </div>
    {/if}
</div>

<style>
    .evenContainer {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        padding:30px;
        background-color: rgb(228, 240, 248);
    }
</style>
