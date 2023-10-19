<script>
    import { page } from '$app/stores';
    import { onMount } from 'svelte';
    import { Chart } from 'svelte-echarts'

    let artist;
    let userData = null;
    let searchQuery = '';
    let pricelist = [];
    let eventDate = '';
    let eventId = '';
    let prediction = ''
    let eventValues = [];
    let sections = [];
    let lowestListings = [];
    let lowestPrice = '';
    let highestPrice = '';
    let totalAvailable = '';
    let options = {};

    onMount(async () => {
        const unsubscribe = page.subscribe(($page) => {
            artist = $page.params.artist;
        });

        try {
            if (artist) {
                const response = await fetch(`http://127.0.0.1:8000/getEventDates/?artist=${artist}`);
                if (response.ok) {
                    userData = await response.json();
                    console.log(userData);
                } else {
                    console.error('Error fetching data:', response.status);
                }
            } else {
                console.error('Artist parameter is not set.');
            }
        } catch (error) {
            console.error('Error:', error);
        }

        return unsubscribe;
    });

    async function getEventValues(eventId, eventDate) {
        try {
            const response = await fetch(`http://127.0.0.1:8000/getLowestListings/?eventid=${eventId}&qty=2&date=${eventDate}`);
            const response2 = await fetch(`http://127.0.0.1:8000/getEventValues/?eventid=${eventId}`)
            if (response.ok) {
                eventValues = await response.json()
                lowestListings = await response2.json()
                lowestPrice = lowestListings.lowestPrice;
                highestPrice = lowestListings.highestPrice;
                totalAvailable = lowestListings.availableamount;
                pricelist = eventValues.newEntry.pricelist;
                prediction = eventValues.newEntry.prediction;
                sections = eventValues.sections;
                //console.log(pricelist, prediction, lowestPrice, highestPrice, totalAvailable, sections)
                options = {
                    color: '#0FBA81',
                    animation: true,
                    colorStops: [{
                    offset: 0, color: 'red' // color at 0%
                    }, {
                    offset: 1, color: 'blue' // color at 100%
                        }],
                    xAxis: {
                        data: Array.from({ length: pricelist.length }, (_, i) => i + 1),
                        type: 'category',
                        show: false
                    },
                    yAxis: {
                        type: 'value',
                        show: false
                    },
                    series: [
                        {
                        data: pricelist,
                        type: 'line'
                        },
                    ],
                    }
                
            } else {
                console.error('Error fetching data:', response.status);
            }
        } catch (error) {
            console.error('Error:', error);
        }
    }

    function filterOptions() {
        if (userData === null) {
            return [];
        }
        return userData.eventlist.filter(date => {
            return date.date.toLowerCase().includes(searchQuery.toLowerCase());
        });
    }

</script>

<main class="relative">
    <div class="container custom-margin mx-auto md:text-left">
        <div class="relative z-[1] space-y-6 pt-20 pb-24">
            <div class="font-bold text-3xl tracking-tight" style="margin-top: -35px;">
                Pick an event date for <span class="text-primary-500">{artist}</span>
            </div>
            
            <input type="text" placeholder="Search date/location" bind:value={searchQuery} class="text-primary-700 border rounded px-2 py-1 mt-4 mb-4 custom-input-width">
            
            {#if userData === null}
                <p>Loading...</p>
            {:else}
            <br>
            <select class="select custom-select-width" size="4" value="1">
                {#each userData.eventlist as date}
                    {#if date.date.toLowerCase().includes(searchQuery.toLowerCase())}
                        <option on:click={getEventValues(date.eventId, date.date)} value={date.date}>{date.date} | {date.eventName}</option>
                    {/if}
                {/each}
            </select>
            {/if}
        </div>
    </div>
    
    <div class="card custom-cardmargin variant-ringed-primary absolute top-20 left-1/2 transform -translate-x-1/2">
        <header class="card-header">Total tickets available</header>
        <section class="p-4"><span class="text-primary-500">{totalAvailable}</span></section>
    </div>

    <div class="card custom-cardmargin variant-ringed-primary absolute top-56 left-1/2 transform -translate-x-1/2">
        <header class="card-header">Lowest price | Highest price</header>
        <section class="p-4"><span class="text-primary-500">{lowestPrice} | {highestPrice}</span></section>
    </div>

    <div class="card custom-cardmargin2 variant-ringed-primary absolute top-20 left-1/2 transform -translate-x-1/2 translate-x-1/2 md:translate-x-48">
        <header class="card-header">Probability of price rising*<br></header>
        <section class="p-4"><span class="text-primary-500 probability">{prediction}%</span></section>
        <footer class="card-footer">*this is just an estimate calculated by a formula based on time until event, total available tickets and floor density.</footer>
    </div>

    <div class="card custom-cardmargin2 variant-ringed-primary absolute top-20 right-0" style="margin-right: 30px;">
        <header class="card-header">Lowest prices per tier</header>
        {#each sections as section}
            <section class="tier-info">{section.name}: <span class="text-primary-500">{section.price[0]}, {section.price[1]}</span></section>
        {/each}
    </div>

    <div class="container md:text-left" style='margin-left: 890px;'>
        <span class="baba">
            Floor spread
        </span>
    </div>
    {#if pricelist.length > 1}
        <div class="app">
            <Chart {options} />
        </div>
    {/if}
</main>
<style>
    
    .app {
      margin-top: -51px;
      width: 100vw;
      height: 60vh;
    }
    .custom-margin {
        margin-left: 50px; 
    }

    .custom-input-width {
        width: 300px; 
    }

    .custom-select-width {
        width: 700px; 
    }

    .custom-cardmargin {
        width: 300px;
    }

    .custom-cardmargin2 {
        width: 350px;
        height: 240px
    }

    .probability {
        font-size: 1.3em;
    }

    .card-footer {
        font-size: 0.8em;
    }

    .tier-info {
        padding: 0px 0; 
        margin-left: 17px;
    }
</style>