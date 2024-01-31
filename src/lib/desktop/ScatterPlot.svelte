<script>
    import { data } from "../../data/data";
    import {scaleLinear, scaleSqrt, range, extent, max, format} from 'd3';    
    import Tooltip from "./Tooltip.svelte";
    
    export let width;
    export let height;    

    let hover = null;
    let hoverCoordinates = null;

    const numberFormat = format("~s");

    const xReference = 5000;
    const yReference = 10000;

    const rAlignArray = ["Italy", "Germany", "Luxembourg", "Romania", "Portugal"];
    const lAlignArray = ["Belgium", "Estonia", "Denmark", "Ireland", "The Netherlands", "United Kingdom"];
    const bAlignArray = ["Sweden", "Finland", "Cyprus", "Bulgaria"];


    $: margin = {top: 20, right: 0, bottom: 40, left: 40};
    $: boundedWidth = width - margin.left - margin.right;
    $: boundedHeight = height - margin.top - margin.bottom;

    $: xScale = scaleLinear()
        .domain([0, max($data, d => d.avg_yearly_tuition_fees)])
        .range([0, boundedWidth]);    

    $: yScale = scaleLinear()
        .domain([0, max($data, d => d.yearly_student_living_costs)])
        .range([boundedHeight, 0]);

    $: rScale = scaleSqrt()
        .domain(extent($data, d => d.total_yearly_living_costs_and_fees))
        .range([8, 8]);


    function handleHover(player, event) {
        hover = player;
        hoverCoordinates = event;
    }
        
</script>

<div class="viz">
    <svg {width} {height}>    
        
        <g transform="translate({margin.left}, {margin.top})">
            <rect 
                x="0"
                y="-32"
                width={xScale(xReference)}
                height={yScale(yReference)+32}
                opacity="0.12"
                class="rect-top-left"
                fill=var(--top-left-box-color)
                />
            <rect 
                x="{xScale(xReference)}"
                y="-32"
                width={xScale(xReference)}
                height={yScale(yReference)+32}
                opacity="0.12"
                class="rect-top-right"
                fill=var(--top-right-box-color)
            />
            <rect 
                x="0"
                y="{yScale(yReference)}"
                width={xScale(xReference)}
                height={boundedHeight-yScale(yReference)}
                opacity="0.12"
                class="rect-bottom-left"
                fill=var(--bottom-left-box-color)
            />
            <rect 
                x="{xScale(xReference)}"
                y="{yScale(yReference)}"
                width={xScale(xReference)}
                height={boundedHeight-yScale(yReference)}
                opacity="0.12"
                class="rect-bottom-right"
                fill=var(--bottom-right-box-color)
            />
            <g>
                {#each range(0, 10001, 1000) as tick, i}
                    <g >
                        <line class="axis-grid-lines" x1="{xScale(tick)}" x2="{xScale(tick)}" y1="-32" y2="{boundedHeight+8}" stroke="black" />
                    </g>
                    <g >
                        <text class="x-axis-label axis-label" x="{xScale(tick)}" y="{boundedHeight+20}">£{numberFormat(tick)}</text>
                    </g>                
                {/each}
            </g>
            <g>
                {#each range(0, 18001, 2000) as tick, i}
                    <g >
                        <line class="axis-grid-lines" x1="-28" x2="{boundedWidth+28}" y1="{yScale(tick)}" y2="{yScale(tick)}" stroke="black" />
                    </g>
                    <g >
                        <text class="y-axis-label axis-label" x="{-4}" y="{yScale(tick)-8}">£{tick === 0 ? format("#")(tick) : numberFormat(tick)}</text>
                    </g>                
                {/each}
            </g>            
            <g>
                {#each $data as d(d.country)}
                    <circle 
                        cx={xScale(d.avg_yearly_tuition_fees)}
                        cy={yScale(d.yearly_student_living_costs)}
                        r={rScale(d.total_yearly_living_costs_and_fees)}                        
                        class="dots"
                        class:hovered={hover ? hover.country === d.country : null}
                        on:mouseover={(e) => handleHover(d, e)}
                        on:mouseout={() => hover = null}
                    />
                    <text
                        x={xScale(d.avg_yearly_tuition_fees) + rScale(d.total_yearly_living_costs_and_fees)+4}
                        y={yScale(d.yearly_student_living_costs) + 3 }                        
                        class="dot-label"                
                        text-anchor="start"        
                    >
                        {#if rAlignArray.includes(d.country)}                            
                            {d.country}
                        {/if}
                    </text>
                    <text
                        x={xScale(d.avg_yearly_tuition_fees) - rScale(d.total_yearly_living_costs_and_fees)-4}
                        y={yScale(d.yearly_student_living_costs) + 2 }                        
                        class="dot-label"                        
                        text-anchor="end"
                    >
                        {#if lAlignArray.includes(d.country)}                            
                            {d.country}
                        {/if}
                    </text>
                    <text
                        x={xScale(d.avg_yearly_tuition_fees)}
                        y={yScale(d.yearly_student_living_costs) + rScale(d.total_yearly_living_costs_and_fees) + 10}                        
                        class="dot-label"    
                        text-anchor="middle"                    
                    >
                        {#if bAlignArray.includes(d.country)}                            
                            {d.country}
                        {/if}
                    </text>
                {/each}
            </g>
            <text
                x={-boundedHeight}
                y={-40}
                class="y-axis-title"
            >
                Yearly Living Costs &nbsp;&#8594;
            </text>
            <text
                x={0}
                y={height-16}
                class="x-axis-title"        
            >
                Average Yearly Tuition Fees &nbsp;&#8594;
            </text>
            <line x1="{xScale(xReference)}" x2="{xScale(xReference)}" y1="-32" y2="{boundedHeight+8}" stroke="black" />
            <line x1="{-28}" x2="{boundedWidth+28}" y1="{yScale(yReference)}" y2="{yScale(yReference)}" stroke="black" />
            <text
                x={12}
                y={4}
                class="top-left-title quadrant-box"        
                dominant-baseline="start"  
                fill=var(--top-left-box-label-color)
            >
                HIGH LIVING & LOW TUITION
            </text>                     
            <text
                x={boundedWidth/2 + 20}
                y={4}
                class="top-right-title quadrant-box"        
                dominant-baseline="start"
                text-anchor="center"   
                fill=var(--top-right-box-label-color)             
            >
                HIGH LIVING & HIGH TUITION
            </text>            
            <text
                x={12}
                y={boundedHeight-20}
                class="bottom-left-title quadrant-box"        
                dominant-baseline="start"  
                fill=var(--bottom-left-box-label-color)              
            >
                LOW LIVING & LOW TUITION
            </text>            
            <text
                x={boundedWidth/2 + 20}
                y={boundedHeight-20}
                class="bottom-right-title quadrant-box"        
                dominant-baseline="start"       
                fill=var(--bottom-right-box-label-color)         
            >
                LOW LIVING & HIGH TUITION
            </text>            
            <!-- <CircleLegend {rScale} boundedHeight={boundedHeight-30} boundedWidth={boundedWidth-20} items={[10000, 15000, 20000, 25000]} /> -->
           
        </g>
    </svg> 
    {#if hover}
        <Tooltip country={hover} {hoverCoordinates} />        
    {/if}     
</div>


<style>
    .quadrant-box {
        font-size: 12px;
        font-weight: 800;
        opacity: 0.5;
    }

    .dot-label {
        font-size: 11px;
        fill: var(--scatter-plot-label-color);
    }

    .y-axis-title {
        transform: rotate(-90deg);
        text-transform: uppercase;
        font-size: 12px;        
        font-weight: 500;
        letter-spacing: 0.6px;
        fill: var(--dark-gray-text);
    }

    .x-axis-title {
        text-transform: uppercase;
        font-size: 12px;
        font-weight: 500;
        letter-spacing: 0.6px;
        fill: var(--dark-gray-text);
        /* text-anchor: end; */
    }

    .viz {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        padding: 20px;
    }

    svg {
        /* border: 1px solid; */
        /* transform: rotate(-45deg); */
        /* transform: translate(0, 400); */
        overflow: visible;
        
    }

    .x-axis-label {
        text-anchor: middle;
    }

    .y-axis-label {
        text-anchor: end;
        dominant-baseline: middle;
    }

    .axis-label {
        font-size: 12px;
        fill: var(--dark-gray-text);
        text-transform: uppercase;
    }

    .axis-grid-lines {
        stroke: rgb(183, 183, 183);
        stroke-dasharray: 2;
   }

   .dots {
    fill: var(--scatter-plot-dot-color);
    opacity: 0.6;
   }

   .hovered {
    stroke: black;
    stroke-width: 2px;
    fill: var(--scatter-plot-dot-hover-color);
    cursor: pointer;
   }
</style>