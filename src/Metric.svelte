<script>
  import { Card } from 'flowbite-svelte';
  import { onMount,afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store


  export let  appData;
   let metrics = null; 
   let startDate = "";
   let endDate = "";

 

   function formatDate(date) {
    const year = date.getFullYear();
    const month = (date.getMonth() + 1).toString().padStart(2, '0');
    const day = date.getDate().toString().padStart(2, '0');
    return `${day}/${month}/${year}`;
  }

  function getLastDayOfMonth(date) {
    const lastDay = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    return lastDay;
  }

  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
    } else {
      
      const today = new Date();

      startDate = formatDate(new Date(today.getFullYear() - 1, today.getMonth(), 1)); // Last year same month first day
      endDate = formatDate(getLastDayOfMonth(today)); // Last day of the current month
    }

   
  }
   // Subscribe to the dateStore
   dateStore.subscribe((value) => {
      startDate = value.startDate;
      endDate = value.endDate;
      fetchData(startDate, endDate); 
  });



    async function fetchData(startDate, endDate) {
      if (startDate && endDate) { 
      try {
       
        const response = await fetch(`${appData.service.endpoint}/dashboard/ats/metrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);
  
        if (response.ok) {
          metrics = await response.json();
      } else {
          const errorText = await response.text();
          console.error("Failed to fetch data. Error:", errorText);
        }
      } catch (error) {
        console.error("Error:", error);
      }
    }
  }
  onMount(() => {
    getDatesFromLocalStorage(); // Try to get dates from local storage
    fetchData(startDate, endDate);
  
  });

  afterUpdate(() => {
  
    localStorage.setItem('startDate', startDate);
    localStorage.setItem('endDate', endDate);
  });
 </script>
{#if metrics !== null}
   <div class="card-container">
       <div class="card"> 
         <span class="hover-text">Total Jobs</span>
         <i class="fa fa-briefcase"></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalJobs}</h5>
         <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Jobs</p>
         </div>

       <div class="card"> <span class="hover-text">Total Placements</span>
         <i class="fa fa-thumbs-o-up"  ></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalPlacements}</h5>
         <p  class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Placements</p>
       </div>

       <div class="card"  style="background-color:blue"> <span class="hover-text">Total Fee/Commission</span>
         <h5 style="color:white;" class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{metrics.totalCommission}</h5>
         <p style="color:white;" class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Billing</p>
       </div>

       <div class="card"><span class="hover-text">Total Job Board Adverts</span>
         <i class="fa fa-bullhorn" ></i> <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalAdverts}</h5>
         <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Adverts</p>
       </div>

       <div class="card"><span class="hover-text">Avg.Time to Fill Days</span>
         <i class="fa fa-clock-o" ></i> <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white" >{metrics.avgTimeToFillDays}</h5>
         <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">TTF</p>
       </div>

       <div class="card">
        <span class="hover-text">Avg.Time to Fill Days</span>
        <i class="fa fa-user" ></i>
        <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white"style="margin-left:15px;">{metrics.avgCandidatesPerPlacement}</h5>
        <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight" >Candidates/Jobs</p>
      </div>
      
   </div>
      
   {/if}
  
    <style>
  .card {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column; /* Stack children vertically */
    width: calc(100vw / 6);
    height: 120px;
    background-color: white;
    margin: 10px;
    padding: 20px;
    box-sizing: border-box;
    margin-top: 120px;
    border-radius: 2px solid black;
    text-align: center;
    position: relative; 
  }

  .card i {
    font-size: 13px;
    margin-right: 62px;
    margin-bottom: -20px;
    text-align: center;
  }

  .card h5 {
    font-size: 24px;
    text-align: center;
  }

  .card p {
    font-size: 16px;
    color: #888c9b;
    font-weight: 500;
    font-size: 18px;
    font-family: var(--tblr-font-sans-serif);
    text-align: center;
    margin-top: -5px;
    margin-left: 10px;
    text-align: center;
  }

  .hover-text {
    display: none;
    position: absolute;
    background-color: rgba(0, 0, 0, 0.7);
    color: white;
    padding: 5px;
    border-radius: 5px;
    top: -50%;
    left: 0;
    right: 0;
    text-align: center;
    font-size: 16px;
    z-index: 1;
  }

  .card:hover .hover-text {
    display: block;
  }

  .card-container {
    display: flex;
    margin: 0 32px;
    justify-content: center;
  }
</style>
