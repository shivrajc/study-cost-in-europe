<script>
  import DataTable from "./DataTable.svelte";
  import Footer from "./Footer.svelte";
     import Header from "./Header.svelte";
  import ScatterPlot from "./ScatterPlot.svelte";


</script>

<main>
    <div class="block-1">
        <div class="header">
            <Header />
        </div>
        <div class="chart-1">
            <p class="scatter-plot-header">Average Tuition Fees vs. Student Living Costs</p>
            <ScatterPlot width={520} height={520}/>
        </div>
    </div>
    <div class="block-2">
        <DataTable width="900" height="800" />
    </div>
    <div class="footer">
        <Footer />
    </div>
</main>

<style>
    main {
        width: 1500px;
        height: 1000px;
        padding: 20px;
        display: grid;
        gap: 40px;
        grid-template-columns: 1fr 1.5fr;
        grid-template-rows: 1fr 60px;
        grid-template-areas: 
        "a b"
        "c c";
    }

    .block-1 {
        grid-area: a;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 20px;

    }
    

    .block-2 {
        grid-area: b;
    }
    .footer {
        grid-area: c;
        border-top: 1px solid gray;        
        padding: 8px 4px;
    }

    .header {
        padding-top: 4px;
    }

    .scatter-plot-header {
        font-size: 24px;
        color: var(--dark-gray-text);
        height: 40px;
        margin: 0 0 12px 0;
    }

    .chart-1 {
        flex-grow: 1;
    }
</style>