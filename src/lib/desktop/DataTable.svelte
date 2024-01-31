<script>
    import { data } from "../../data/data";
    import {scaleLinear, scaleBand, extent, format} from 'd3';    

    export let width;
    export let height;    

    let hover = null;

    const barChartOffset = 460;

    let selectedCountry = "Estonia";
    let selectedMetric = "total_yearly_living_costs_and_fees";

    $: selectedMetricValue = $data.filter(d => d.country === selectedCountry)[0][selectedMetric];

    $: selectedMetricArray = $data.map(d => d[selectedMetric] - selectedMetricValue);    

    $: xScale = scaleLinear()
        .domain(extent(selectedMetricArray))
        .range([0, 360]);

    $: yScale = scaleBand()
        .domain($data.map(d => d.country))
        .range([0, height]);

    $: console.log(xScale.domain())

    const numberFormat = format("#,");    
</script>

<div class="column-header-container">
    <span class="column-header country-column-header">COUNTRY</span>
    <span class="column-header total-column-header">TOTAL YEARLY LIVING + FEES</span>
    <span class="column-header avg-column-header">AVG YEARLY TUITION FEES</span>
    <span class="column-header living-column-header">YEARLY LIVING COSTS</span>
    <span class="column-header comp-column-header">TOTAL COST COMPARISON: <span class="country-label">{selectedCountry}</span> <span style="text-transform: lowercase;">vs.</span> OTHERS </span>
</div>
<svg {width} {height}>

    {#each $data as d}
        <g class="row" transform="translate(0,0)">
            <rect
                x={0}
                y={yScale(d.country)}
                width={width}
                height={yScale.bandwidth()}
                opacity="0"
                class="row-background"
                class:selected-row={d.country === selectedCountry}
                class:hovered={hover ? d.country === hover.country : null}
                on:mouseover={() => hover = d}
                on:mouseout={() => hover = null}
                on:click={() => selectedCountry = d.country}
            />
            <line 
                x1={0}
                x2={width}
                y1={yScale(d.country) + yScale.bandwidth()}
                y2={yScale(d.country) + yScale.bandwidth()}
                stroke="hsl(204, 20%, 92%)"
            />            
            <g class="row" transform="translate(20,0)">            
            <text
                x="0"
                y="{yScale(d.country) + yScale.bandwidth() / 2}"
                dominant-baseline="middle"    
                class:selected-row-text-color={d.country === selectedCountry}            
            >                
                {d.country}
            </text>
            <text
                x="200"
                y="{yScale(d.country) + yScale.bandwidth() / 2}"
                dominant-baseline="middle"
                text-anchor="end"
                class:selected-row-text-color={d.country === selectedCountry}
            >
                £{numberFormat(d.total_yearly_living_costs_and_fees)}
            </text>
            <text
                x="300"
                y="{yScale(d.country) + yScale.bandwidth() / 2}"
                dominant-baseline="middle"
                text-anchor="end"
                class:selected-row-text-color={d.country === selectedCountry}
            >
                £{numberFormat(d.avg_yearly_tuition_fees)}
            </text>
            <text
                x="400"
                y="{yScale(d.country) + yScale.bandwidth() / 2}"
                dominant-baseline="middle"
                text-anchor="end"
                class:selected-row-text-color={d.country === selectedCountry}
            >
                £{numberFormat(d.yearly_student_living_costs)}
            </text>
            <g transform="translate({barChartOffset}, 0)">
            <rect 
                x={xScale(Math.min(0, (d[selectedMetric] - selectedMetricValue)))}
                y={yScale(d.country)}
                width={Math.abs((xScale(d[selectedMetric] - selectedMetricValue)) - xScale(0))}
                height={yScale.bandwidth()-2}
                opacity="1"
                rx="2"
                style="pointer-events: none;transition: all 0.2s ease;"
                fill={(d[selectedMetric] - selectedMetricValue) < 0 ? "var(--bar-chart-green-color)" : "var(--bar-chart-red-color)"}                
            />
            <text 
                x={ (d[selectedMetric] - selectedMetricValue) < 0 ? xScale(0) - 8 : xScale(0) + 8}
                y={yScale(d.country) + yScale.bandwidth() / 2}            
                dominant-baseline="middle"
                fill={(d[selectedMetric] - selectedMetricValue) < 0 ? "darkgreen" : "darkred"}
                text-anchor={(d[selectedMetric] - selectedMetricValue) < 0 ? "end" : "start"}                
            >                
                {d.country === selectedCountry ? "" : (d[selectedMetric] - selectedMetricValue) < 0 ? "- £" : "+ £"}
                {d.country === selectedCountry ? "" : numberFormat(Math.abs(d[selectedMetric] - selectedMetricValue))}
            </text>
            </g>
            </g>
        </g>
    {/each}
    <line 
        x1={xScale(0)+barChartOffset+20}
        x2={xScale(0)+barChartOffset+20}
        y1={0}
        y2={height}
        stroke-width="2"
        stroke="var(--table-row-selected-color)"
        stroke-dasharray="2"
    />    
    <text 
        x={20}
        y={Number(height) + 28}            
        fill="hsl(204, 4%, 60%)"
        font-size="14px"
    >                
    Click on any row to select that country
    </text>    
</svg>

<style>
    .column-header-container {
        display: flex;
        justify-content: flex-start;
        align-items: end;
        font-size: 13px;        
        font-weight: 700;
        gap: 20px;
        height: 60px;
        padding-left: 20px;
        padding-bottom: 8px;
        margin-bottom: 4px;
        border-bottom: 1px solid;
        color: var(--dark-gray-text);
        letter-spacing: 0.1px;
    }

    .country-column-header {
        width: 90px;
    }

    .total-column-header {
        width: 90px;
        text-align: right;
    }

    .avg-column-header {
        width: 80px;
        text-align: right;
    }

    .living-column-header {
        width: 80px;
        text-align: right;
    }

    .comp-column-header {
        flex-grow: 1;
        text-align: left;
        text-transform: uppercase;
        padding-left: 20px;

    }

    svg {
        overflow: visible;
    }
    .hovered {
        opacity: 1;
    }
    .row-background {
        cursor: pointer;
        fill: var(--table-hover-color);
    }
    text {
        pointer-events: none;
    }

    .selected-row-text-color {
        fill: var(--table-row-selected-label-color);
    }

    .selected-row {
        fill: var(--table-row-selected-color);
        opacity: 1;
    }

    .country-label {
        background-color: var(--table-row-selected-color);
        color: var(--table-row-selected-label-color);
        padding: 4px 8px;        
    }
</style>