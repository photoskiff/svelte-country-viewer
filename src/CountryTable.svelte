<script lang="ts">
    import type { Country, Currency } from "./model/country";
    type Order = "asc" | "desc" | undefined;
    type ActiveField = keyof Pick<
        Country,
        "name" | "alpha2Code" | "alpha3Code" | "currencies"
    >;
    const filterFields: ActiveField[] = [
        "name",
        "alpha2Code",
        "alpha3Code",
        "currencies",
    ];

    type FieldFilter =
        | ((word: string, country: Country) => boolean)
        | undefined;
    const simpleFilter = (word: string, field: string) =>
        field?.toLowerCase().includes(word.toLowerCase());

    const getFilter = (field: ActiveField): FieldFilter => {
        if (!field) return undefined;
        if (field !== "currencies")
            return (word, country) => simpleFilter(word, country[field]);
        else if (field === "currencies")
            return (word, country) =>
                country.currencies.some((c) => simpleFilter(word, c?.code));
        return undefined;
    };

    const createFilterCriteria = (field: ActiveField) => {
        return {
            field,
            prompt: `type to filter by ${field}, press 'Esc' to reset, '/' or '\\' to rotate filter`,
            func: getFilter(field),
        };
    };

    export let countries: Country[] = [];

    let filterCriteria = createFilterCriteria("name");
    let filterEntered = "";

    const rotateFilterCriteria = (val: string) => {
        if (!val || !"\\/".includes(val)) return val;
        let max = filterFields.length - 1;
        let index = filterFields.indexOf(filterCriteria.field);
        if (val === "/") index = index === max ? 0 : index + 1;
        else index = index === 0 ? max : index - 1;
        filterCriteria = createFilterCriteria(filterFields[index]);
        filterEntered = "";
        return "";
    };
    $: filterWord = rotateFilterCriteria(filterEntered);
    $: filtered = countries.filter((c) => filterCriteria.func(filterWord, c));

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
</script>

<div>
    <input bind:value={filterEntered} placeholder={filterCriteria.prompt} />
    <table>
        <tr>
            <th /><th>Country</th><th>Population</th><th>Alpha2</th><th
                >Alpha3</th
            ><th>Capital</th><th>Currency</th>
        </tr>
        {#each filtered as { name, population, alpha2Code, alpha3Code, flag, capital, currencies }}
            <tr>
                <td><img width="14" height="14" alt="flag" src={flag} /></td>
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
    input {
        width: 500px;
    }
    table {
        font-family: arial, sans-serif;
        border-collapse: collapse;
        width: 60%;
        display: block;
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
