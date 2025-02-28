<script>
    import { onMount } from "svelte";
    import { writable, get } from "svelte/store";
    import PatientRecord from "./record-patient.svelte";

    let selectedGender = "All";
    let date = new Date().toISOString().split("T")[0];
    let attendees = writable([]);
    let sortKey = writable("name");
    let sortOrder = writable("asc");
    let newPatient = { name: "", gender: "Male", datetime: "" };
    let showModal = writable(false);

    const genders = ["All", "Male", "Female"];
    const tableHeaders = [
        { key: "name", label: "Name" },
        { key: "gender", label: "Gender" },
        { key: "datetime", label: "Date & Time" },
    ];

    const patients = [
        { name: "Alice Gou", gender: "Female", datetime: "2025-02-28 14:30" },
        { name: "Bob Ackerman", gender: "Male", datetime: "2025-02-28 15:00" },
        { name: "Charlie Johnson", gender: "Male", datetime: "2025-02-28 15:45" },
        { name: "David Kelly", gender: "Male", datetime: "2025-02-28 16:15" },
        { name: "Eve Nighcast", gender: "Female", datetime: "2025-02-28 17:00" },
        { name: "Frank Mendez", gender: "Male", datetime: "2025-02-28 18:00" },
    ];

    function filterData() {
        const filtered = patients.filter(person => {
            let personDate = person.datetime.split(" ")[0];
            return (selectedGender === "All" || person.gender === selectedGender) &&
                   (personDate === date);
        });
        attendees.set(filtered);
    }

    function sortBy(key) {
        if (get(sortKey) === key) {
            sortOrder.update(o => (o === "asc" ? "desc" : "asc"));
        } else {
            sortKey.set(key);
            sortOrder.set("asc");
        }

        attendees.update(items => {
            return [...items].sort((a, b) => {
                let valA = a[key];
                let valB = b[key];

                if (typeof valA === "string") {
                    return valA.localeCompare(valB) * (get(sortOrder) === "asc" ? 1 : -1);
                } else {
                    return (valA - valB) * (get(sortOrder) === "asc" ? 1 : -1);
                }
            });
        });
    }

    function addPatient() {
    if (!newPatient.name || !newPatient.datetime) return;
    
    patients.push({ ...newPatient });
    filterData();
    
    // Reset form data
    newPatient.name = "";
    newPatient.gender = "Male";
    newPatient.datetime = "";
}

    onMount(() => {
        filterData();
    });
</script>

<div class="p-6 max-w-6xl mx-auto bg-white rounded-xl shadow-lg">
    <h1 class="text-3xl font-bold text-blue-600 mb-4">Adult History Sheet System</h1>

    <div class="flex items-center gap-4 mb-6">
        <select bind:value={selectedGender} on:change={filterData}
            class="p-2 w-22 border border-gray-300 rounded-md text-gray-700 shadow-sm focus:ring-2 focus:ring-blue-500">
            {#each genders as gender}
                <option value={gender}>{gender}</option>
            {/each}
        </select>

        <input type="date" bind:value={date} on:change={filterData}
            class="p-2 border border-gray-300 rounded-md text-gray-700 shadow-sm focus:ring-2 focus:ring-blue-500" />
        
        <button on:click={() => showModal.set(true)}
            class="p-2 bg-blue-500 text-white rounded-md shadow hover:bg-blue-600 transition">Add Patient</button>
    </div>

    {#if $showModal}
    <PatientRecord
        bind:newPatient={newPatient}
        bind:showModal={showModal}
        {genders}
        {addPatient}
    />
{/if}


    <div class="max-h-[65vh] overflow-y-auto w-full">
        <table class="w-full border-collapse bg-white rounded-lg text-sm">
            <thead class="sticky top-0 z-10">
                <tr class="bg-blue-500 text-white">
                    {#each tableHeaders as header}
                        <th class="p-2 cursor-pointer hover:bg-blue-500 transition" on:click={() => sortBy(header.key)}>
                            {header.label}
                            <span class="ml-1 text-xs">{($sortKey === header.key && $sortOrder === "asc") ? "▲" : "▼"}</span>
                        </th>
                    {/each}
                </tr>
            </thead>
            <tbody>
                {#each $attendees as person}
                    <tr class="border-b last:border-none bg-gray-50 hover:bg-blue-100 transition">
                        <td class="p-2">{person.name}</td>
                        <td class="p-2 text-center">{person.gender}</td>
                        <td class="p-2 text-center">{person.datetime}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>
