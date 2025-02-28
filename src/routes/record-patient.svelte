<script>
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';
  
    export let showModal = writable(false);
    export let newPatient;
    export let addPatient;
    export let genders;
  </script>
  
  {#if $showModal}
    <div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center">
      <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-lg overflow-y-auto max-h-[80vh]">
        <header class="text-center mb-4">
          <h2 class="text-lg font-semibold">NABUNTURAN DOCTORS HOSPITAL</h2>
          <p class="text-sm">Echavez St., Purok 10, Poblacion, Nabunturan, Davao de Oro</p>
          <h1 class="text-xl font-bold mt-2">ADULT HISTORY SHEET</h1>
        </header>
        
        <section class="grid grid-cols-2 gap-4">
          <input type="text" bind:value={newPatient.name} placeholder="Name" class="input"/>
          <select bind:value={newPatient.gender} class="input">
            {#each genders as gender}
              <option value={gender}>{gender}</option>
            {/each}
          </select>
          <input type="number" bind:value={newPatient.age} placeholder="Age" class="input"/>
          <input type="text" bind:value={newPatient.ward} placeholder="Ward" class="input"/>
          <input type="text" bind:value={newPatient.hospitalNo} placeholder="Hospital No." class="input"/>
        </section>
  
        <section class="mt-4">
          <h3 class="font-semibold">Complaints</h3>
          <textarea bind:value={newPatient.complaint1} placeholder="Complaint 1" class="input"></textarea>
          <textarea bind:value={newPatient.complaint2} placeholder="Complaint 2" class="input"></textarea>
          <textarea bind:value={newPatient.complaint3} placeholder="Complaint 3" class="input"></textarea>
        </section>
  
        <section class="mt-4">
          <h3 class="font-semibold">Past Medical History</h3>
          <label><input type="checkbox" bind:checked={newPatient.hospitalization}/> Previous Hospitalization</label>
          {#if newPatient.hospitalization}
            <input type="text" bind:value={newPatient.hospitalizationCount} placeholder="No. of Times" class="input"/>
          {/if}
          <input type="text" bind:value={newPatient.diagnosis1} placeholder="Diagnosis 1" class="input"/>
          <input type="text" bind:value={newPatient.diagnosis2} placeholder="Diagnosis 2" class="input"/>
          <input type="text" bind:value={newPatient.diagnosis3} placeholder="Diagnosis 3" class="input"/>
        </section>
  
        <div class="flex justify-end mt-6 gap-4">
          <button on:click={() => $showModal = false} class="px-4 py-2 bg-gray-400 text-white rounded-md">Cancel</button>
          <button on:click={addPatient} class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition">Save</button>
        </div>
      </div>
    </div>
  {/if}
  
  <style>
    .input {
      width: 100%;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  </style>
  