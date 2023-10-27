<script>
    import { onMount, onDestroy } from "svelte";
    import { Browser } from '@syncfusion/ej2-base';
    import { DashboardLayout } from '@syncfusion/ej2-layouts';
    import { Chart, LineSeries, DateTime, Legend, Tooltip, ColumnSeries, DataLabel, Category, Highlight } from '@syncfusion/ej2-charts';
    import { SplineAreaSeries } from '@syncfusion/ej2-charts';

    Chart.Inject(LineSeries, DateTime, Legend, Tooltip, Highlight, ColumnSeries, DataLabel, Category, SplineAreaSeries);

    let dashboardObject;
    let chartObj1;
    let chartObj2;
    let localStorageKey = 'chartPositions';

    onMount(() => {
        // Initialize the DashboardLayout
        dashboardObject = new DashboardLayout({
            cellSpacing: [15, 15],
            cellAspectRatio: Browser.isDevice ? 1 : 0.8,
            columns: Browser.isDevice ? 2 : 8,
            panels: [
                {
                    'sizeX': Browser.isDevice ? 1 : 5, 'sizeY': Browser.isDevice ? 1 : 2, 'row': 0, 'col': 0,
                    header: '<div class="title" id="header1">Sales - Yearly Performance</div>', content: '<div id="columnchart1" style="height:100%; width:100%"></div>'
                },
                {
                    'sizeX': Browser.isDevice ? 1 : 3, 'sizeY': Browser.isDevice ? 1 : 2, 'row': 0, 'col': Browser.isDevice ? 1 : 5,
                    header: '<div class="title" id="header2">Product Wise Sales - 2021</div>', content: '<div id="Columnchart2" style="height:100%; width:100%"></div>'
                },
            ]
        });

        // Update the DashboardLayout with the loaded positions
        dashboardObject.appendTo('#defaultLayout');

        // Initialize the LineSeries chart
        chartObj1 = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
                labelStyle: { size: '11px' }
            },
            chartArea: { border: { width: 0 } },
            primaryYAxis: {
                minimum: 0,
                maximum: 100,
                majorTickLines: { width: 0 },
                labelFormat: '{value}%',
                lineStyle: { width: 0 },
                labelStyle: { size: '11px' },
                titleStyle: { size: '13px' },
            },
            width: '99%',
            height: '100%',
            series: [
                {
                    dataSource: [
                        { Period: '2017', Percentage: 60 },
                        { Period: '2018', Percentage: 56 },
                        { Period: '2019', Percentage: 71 },
                        { Period: '2020', Percentage: 85 },
                        { Period: '2021', Percentage: 73 },
                    ],
                    type: "Column",
                    name: "Online",
                    xName: "Period",
                    yName: "Percentage",
                    fill: '#2485FA',
                    marker: { dataLabel: { visible: true, position: 'Middle', font: { color: 'white' } }
                    }
                },
            ],
        });

        chartObj1.appendTo('#columnchart1');

        // Initialize the ColumnSeries chart
        chartObj2 = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
                labelStyle: { size: '11px' }
            },
            chartArea: { border: { width: 0 } },
            primaryYAxis: {
                minimum: 0,
                maximum: 100,
                majorTickLines: { width: 0 },
                labelFormat: '{value}%',
                lineStyle: { width: 0 },
                labelStyle: { size: '11px' },
                titleStyle: { size: '13px' },
            },
            width: '99%',
            height: '100%',
            series: [
                {
                    dataSource: [
                        { Period: '2017', Percentage: 40 },
                        { Period: '2018', Percentage: 44 },
                        { Period: '2019', Percentage: 29 },
                        { Period: '2020', Percentage: 15 },
                        { Period: '2021', Percentage: 27 },
                    ],
                    type: "Column",
                    name: "Retail",
                    xName: "Period",
                    yName: "Percentage",
                    fill: '#FEC200',
                    marker: { dataLabel: { visible: true, position: 'Middle', font: { color: 'white' } }
                    }
                },
            ],
        });

        chartObj2.appendTo('#Columnchart2');
    });

    onDestroy(() => {
        // Save chart positions to local storage
        const chartPositions = {};
        dashboardObject.panels.forEach(panel => {
            chartPositions[panel.id] = {
                row: panel.row,
                col: panel.col,
                sizeX: panel.sizeX,
                sizeY: panel.sizeY,
            };
        });

        localStorage.setItem(localStorageKey, JSON.stringify(chartPositions));

        // Log chart positions to the console
        console.log('Chart Positions:', chartPositions);
    });
</script>

<div class="control-section">
    <div id="defaultLayout"></div>
</div>
