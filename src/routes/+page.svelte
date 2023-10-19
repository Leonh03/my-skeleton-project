<script lang="ts">
    import { debounce } from 'lodash';
	import { goto } from '$app/navigation';

    let searchTerm = "";
    let error = '';
    let searchQuery = '';
    let searchData = [];
    const DEBOUNCE_DELAY = 300;

	const navigateToReceiver = (artist: any) => {
    goto(`/datesPage/${artist}`);
  	};

    function navigateToError() {
        const pageUrl = '/error/notFound'
        window.location.href = pageUrl;
    }

    async function search() {
        try {
            const response = await fetch(`http://127.0.0.1:8000/getEventList/?artist=${searchQuery}`);
            if (response.ok) {
                searchData = await response.json();
            } else {
                console.error('Error fetching data:', response.status);
            }
        } catch (error) {
            console.error('Error:', error);
        }
    }

    const debouncedSearch = debounce(search, DEBOUNCE_DELAY);

    function handleInput(event) {
        searchQuery = event.target.value;
        debouncedSearch();
    }


</script>

<div class="container mx-auto text-left p-4 md:text-center">
    <div class="relative z-[1] space-y-6 pt-32 pb-24">
        <div class="font-bold text-5xl tracking-tight">
            Your solution to all <span class="text-primary-500">ticket tracking</span> needs
        </div>
        <p class="max-w-full opacity-70 md:mx-auto">
            Search any event and get the lowest prices for tickets in seconds <br>
			Try it for yourself <i class="arrow down"></i>
        </p>
        <div class="flex flex-wrap gap-3 md:space-x-3 md:block">
            <input
                type="text"
                class="block w-full px-4 py-2 text-blue-700 bg-white border rounded-md focus:border-blue-400 focus:ring-blue-300 focus:outline-none focus:ring focus:ring-opacity-40"
                placeholder="Search for Events, Artists..."
                bind:value={searchQuery}
                on:input={handleInput}
            />

            {#if searchData.topResult === undefined}
            <div>
                <ul>
                    <li></li>
                </ul>
            </div>
            {:else}
			<a class="block card card-hover p-4" href="#" on:click={navigateToReceiver(searchData.topResult)}>
				<ul>
					<header class="text-primary-500">{searchData.topResult}</header>
					<section>Browse {searchData.topResult} tickets.</section>
				</ul>
			</a>
				{#each searchData.performers as performer}
				<a class="block card card-hover p-4" href="#" on:click={navigateToReceiver(performer.name)}>
					<ul>
						<header class="text-primary-500">{performer.name}</header>
						<section>Browse {performer.name} tickets.</section>
					</ul>
				</a>
				{/each}
                <a class="block card card-hover p-4" href="#" on:click={navigateToError}>
					<ul>
						<header class="text-error-500">Cant find what you're looking for?</header>
						<section>Contact us here.</section>
					</ul>
				</a>
            {/if}

        </div>
    </div>
</div>

<style>
    .arrow {
        border: solid limegreen;
        border-width: 0 3px 3px 0;
        display: inline-block;
        padding: 3px;
    }
    .down {
        transform: rotate(45deg);
        -webkit-transform: rotate(45deg);
    }
	.card {
		text-align: left;
	}
	
</style>