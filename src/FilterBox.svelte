<script lang="ts">
    import type { Country } from "./model/country";
    import { beforeUpdate } from "svelte";

    type Order = "asc" | "desc" | undefined;
    let order:Order = undefined;
    

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
    let selectedField: ActiveField = "name";
    $: filterCriteria = createFilterCriteria(selectedField);
    let filterEntered = "";

    const rotateFilterCriteria = (val: string) => {
        if (!val || !"\\/".includes(val)) return val;
        let max = filterFields.length - 1;
        let index = filterFields.indexOf(filterCriteria.field);
        if (val === "/") index = index === max ? 0 : index + 1;
        else index = index === 0 ? max : index - 1;
        selectedField = filterFields[index];
        filterCriteria = createFilterCriteria(selectedField);
        filterEntered = "";
        return "";
    };

    export let filtered: Country[] = [];
    $: filterWord = rotateFilterCriteria(filterEntered);
    $: sorted = order ?  countries?.sort((a, b) => order === "asc" ? a.population - b.population : b.population - a.population) : countries;
    $: filtered = sorted.filter((c) => filterCriteria.func(filterWord, c));

    const handleKeyPress = (e: KeyboardEvent) => {
        if (e.key === "Escape") {
            filterEntered = "";
        }
    };

    let box;
    beforeUpdate(() => box?.focus());
</script>

<div class="container">
    <input
        bind:value={filterEntered}
        bind:this={box}
        placeholder={filterCriteria.prompt}
        on:keydown={handleKeyPress}
    />
    <select bind:value={selectedField}>
        {#each filterFields as f}
            <option value={f}>{f}</option>
        {/each}
    </select>
    <button on:click="{() => order = 'asc'}" disabled={order === 'asc'}>asc</button>
    <button on:click="{() => order = undefined}" disabled={!order}>reset</button>
    <button on:click="{() => order = 'desc'}" disabled={order === 'desc'}>desc</button>
</div>

<style>
    .container {
        display: block;
    }
    input {
        width: 500px;
    }
    select {
        height: 37px;
    }
</style>
