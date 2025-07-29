<script lang="ts">
    import {onMount} from 'svelte';

    interface Event {
        eventKey: string;
        date: string;
        'event-name': string;
        location: string;
        image: string;
        slug: string;
        description: string;
    }

    let events = $state<Event[]>([]);
    let error = $state<string | null>(null);

    // IMPORTANT: Replace with your actual API Gateway Invoke URL
    const API_URL = '';

    async function fetchEvents(): Promise<void> {
        error = null;
        const response = await fetch(API_URL);
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        events = await response.json();
    }

    onMount(() => {
        fetchEvents();
    });
</script>

<div class="min-h-screen bg-gray-100 p-6 flex flex-col items-center font-inter">
    <h1 class="text-5xl font-extrabold text-gray-900 mb-10 text-center">
        Club Events
    </h1>

    {#if error}
        <div class="bg-red-100 border-l-4 border-red-500 text-red-700 p-8 rounded-lg shadow-lg max-w-2xl w-full text-center">
            <h2 class="text-3xl font-bold mb-4">Error Loading Data!</h2>
            <p class="text-xl">There was an issue fetching the events. Please check your network connection or the API
                endpoint.</p>
            <p class="text-lg mt-2">Error details: {error}</p>
            <button
                    onclick={fetchEvents}
                    class="mt-4 bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 transition-colors duration-300"
            >
                Retry
            </button>
        </div>
    {:else if events.length === 0}
        <div class="bg-yellow-100 border-l-4 border-yellow-500 text-yellow-700 p-10 rounded-lg shadow-lg max-w-3xl w-full text-center">
            <h2 class="text-4xl font-extrabold mb-6">
                <span class="text-5xl mr-2">⚠️</span> No Data Found!
            </h2>
            <p class="text-2xl leading-relaxed">
                <strong class="text-yellow-800">You needs to build the cloud infrastructure first!</strong>
                <br/>
                Ensure your Lambda function is deployed, the API Gateway is configured correctly, and the DynamoDB table
                has data.
            </p>
        </div>
    {:else}
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl">
            {#each events as event (event.eventKey)}
                <div class="bg-white rounded-xl shadow-lg hover:shadow-xl transition-shadow duration-300 overflow-hidden flex flex-col">
                    <img
                            src={event.image || `https://placehold.co/600x400/FFD700/000000?text=${encodeURIComponent(event['event-name'] || 'No Image')}`}
                            alt={event['event-name']}
                            class="w-full h-48 object-cover rounded-t-xl"
                            loading="lazy"
                    />
                    <div class="p-6 flex flex-col flex-grow">
                        <h2 class="text-3xl font-bold text-gray-900 mb-3">{event['event-name']}</h2>
                        <p class="text-lg text-gray-700 mb-2">
                            <strong>Date:</strong> {event.date}
                        </p>
                        <p class="text-lg text-gray-700 mb-4">
                            <strong>Location:</strong> {event.location}
                        </p>
                        <p class="text-md text-gray-600 flex-grow">{event.description}</p>
                        <a
                                href={`#${event.slug}`}
                                class="mt-6 inline-block bg-blue-600 text-white font-semibold py-3 px-6 rounded-lg shadow-md hover:bg-blue-700 transition-colors duration-300 text-center"
                        >
                            Learn More
                        </a>
                    </div>
                </div>
            {/each}
        </div>
    {/if}
</div>
