<script>
  import { onMount, afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store
  import { Chart, BarSeries, Category, DataLabel } from '@syncfusion/ej2-charts';
  Chart.Inject(BarSeries, Category, DataLabel);



  export let  appData;
  let chartData = [];
  let chart = null; // Initialize chart with null
  

  let startDate = '';
  let endDate = '';


 
  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
     
    }

    console.log('Stored startDate:', startDate);
    console.log('Stored endDate:', endDate);
  }

  // Subscribe to the dateStore
  dateStore.subscribe((value) => {
    startDate = value.startDate;
    endDate = value.endDate;
    fetchData(startDate, endDate); // Fetch data whenever the date changes
  });

  async function fetchData(startDate, endDate) {
    if (startDate && endDate) { 
    try {
    
      const response = await fetch(`${appData.service.endpoint}/dashboard/ats/data/rejectreasons?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);

      if (response.ok) {
        const jsonData = await response.json();
        console.log("Data fetched:", jsonData);
        chartData = jsonData.slice(0, 6).map((item, index) => ({
          x: item.reason || 'Unknown',
          y: item.count,
        
        }));
        chartData.sort((a, b) => a.y - b.y);
        updateChart();
      } else {
        const errorText = await response.text();
        console.error("Failed to fetch data. Error:", errorText);
      }
    } catch (error) {
      console.error("Error:", error);
    }
  }
  }

  function updateChart() {
    const data = chartData.map(item => ({ ...item }));
    if (chart) {
      chart.destroy();
    }
    const maxValue = Math.max(...chartData.map(item => item.y));
  const maxRoundedTo5 = Math.ceil(maxValue / 5) * 5;

  // Calculate the maximum value for the Y-axis, rounding it up to the nearest multiple of 5
  const yAxisMax = maxRoundedTo5 + 1;
    chart = new Chart({
      primaryXAxis: {
        valueType: 'Category',
        majorGridLines: { width: 0 },
        labelStyle: {
            size: '14px',
            fontWeight: 'normal',
        },
      },
      primaryYAxis: {
        majorTickLines: { width: 0 },
        minorTickLines: { width: 0 },
        lineStyle: { width: 0 },
        
      maximum: yAxisMax, 
        labelStyle: {
          size: '1rem',
          fontWeight: "400",
          fontFamily: 'sans-serif'
        },
      },
      height: '350px',
      series: [
        {
          type: 'Bar',
          dataSource: chartData,
          xName: 'x',
          width: 2,
          yName: 'y',
          columnSpacing: 0.1,
          animation: {
            enable: true,
            duration: 1000,
            delay: 200
          },
          cornerRadius: {
            topRight: 5,
            bottomRight: 5
          },
          marker: {
            dataLabel: {
              visible: true,
              position: "Bottom",
              font: {
                fontWeight: 'bold',
                color: 'white',
                size: '17px'
              }
            }
          },
        },
      ],
      
    });

    chart.appendTo('#container');
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


<div class="card card-fluid">
  <div class="card-header border-0">
      Reject Reasons
  </div>
  <div class="card-body">
      <div id="container"></div>
  </div>
</div>


<style>
 .card {
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0;
    background-color: #fff;
    background-clip: border-box;
    border: 1px solid rgba(20,20,31,.12);
    border-radius: 0.25rem;
    margin: 40px;
    margin-top:-20px;
    

}

.card-body {
    flex: 1 1 auto;
    min-height: 1px;
    padding: 1rem;
}
.card-header {
  font-size: 16px; /* Adjust the font size as needed */
  font-weight: bold; /* Adjust the font weight as needed */
  padding: 15px; /* Adjust the padding as needed */
 
}

</style>