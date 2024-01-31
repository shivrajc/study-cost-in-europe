<script>  
  import { format } from 'd3';

    const numberFormat = format("#,");
    export let country;
    export let hoverCoordinates;
</script>

<div class="tooltip" style="position:absolute; top:{hoverCoordinates.pageY}px; left:{hoverCoordinates.pageX+16}px">
    <p class="main-text">{country.country}</p>
    <p class="sub-text">Total Yearly Living Costs & Fees: <span>£{numberFormat(country.total_yearly_living_costs_and_fees)}</span></p>
    <p class="sub-text">Average Yearly Tuition Fees: <span>£{numberFormat(country.avg_yearly_tuition_fees)}</span></p>
    <p class="sub-text">Yearly Student Living Costs: <span>£{numberFormat(country.yearly_student_living_costs)}</span></p>
</div>

<style>
    .tooltip {
        padding: 16px;
        pointer-events: none;
        transition: top 300ms, left 300ms;
        width: fit-content;
        background: white;
        border-radius: 4px;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 4px;                
        box-shadow: rgba(0, 0, 0, 0.25) 0px 0.0625em 0.0625em, rgba(0, 0, 0, 0.25) 0px 0.125em 0.5em, rgba(255, 255, 255, 0.1) 0px 0px 0px 1px inset;
    }

    p {
        margin: 0;
        padding: 0;
        text-align: left;
    }

    .main-text {
        font-size: 1.4rem;
        font-weight: bold;
        margin-bottom: 8px;
        color: var(--dark-gray-text);
    }

    .sub-text {
        font-size: 18px;
        font-weight: 500;
        color: hsl(200, 4%, 40%);
    }

    span {
        font-weight: bold;
        font-size: 20px;
        color: var(--dark-gray-text);
    }
</style>
