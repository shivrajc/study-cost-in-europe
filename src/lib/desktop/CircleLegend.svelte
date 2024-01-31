<script>
    import { format } from 'd3';

    export let rScale;
    export let items;
    export let boundedHeight;
    export let boundedWidth;
</script>

<g>
    <rect 
        x={boundedWidth - 82}
        y={boundedHeight-70}
        width={96}
        height={90}
        fill="white"
        opacity="0.6"
    />
    <text
        x={boundedWidth - 76}
        y={84}  
        class="legend-title"      
    >TOTAL EARNINGS</text>
    {#each items as item, i}        
        <circle 
            cx={boundedWidth - 56} 
            cy={boundedHeight - rScale(item)} 
            r={rScale(item)} 
            class="circle"
            stroke="black"
        />
        
        <line 
            x1={boundedWidth + rScale(item) - (55 + i)}
            x2={boundedWidth + 40 - 56}
            y1={boundedHeight - rScale(item) - (i*2)}
            y2={boundedHeight - rScale(item)- (i*2)}
            class="line"
        />

        <text 
            x={boundedWidth + 40 - 54} 
            y={boundedHeight - rScale(item) - (i*2)} 
            r={rScale(item)} 
            class="text"
            alignment-baseline="middle"
        >{format('~s')(item)}</text>
    {/each}
</g>

<style>
    .legend-title {
        font-size: 10px;
        font-weight: 600;
        fill: var(--label-light-grey);
        letter-spacing: 0.2px;
    }
    .circle {
        fill: none;
        /* stroke: var(--label-light-grey); */
    }
    
    .line {
        stroke: black;   
        stroke-width: 0.8;
    }
    
    .text {        
        fill: var(--label-light-grey);   
        font-size: 0.3rem;
        font-weight: bold;        
    }
</style>