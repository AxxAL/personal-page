<template>
    <div class="h-screen flex items-center justify-center">

        <div v-if="!currentArt.image">
            <img src="/icons/gear-spinner-white.svg" class="animate-spin-slow h-16 w-16 text-color mx-auto" />
            <p class="text-white animation-vcr text-center">Searching for art...</p>
        </div>
        

        <div v-else v-cloak class="p-4 rounded-xl flex flex-col items-center space-y-4">
            
            <h1 class="text-white text-center animation-vcr">{{ currentArt.artist }}</h1>

            <img @click="randomizeCurrentArt" title="Click me to see new art!" :src="currentArt.image" class="max-h-160 max-w-4xl hover:cursor-pointer" />

            <p class="text-white text-center animation-vcr">"{{ currentArt.title }}"</p>
        
        </div>

    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
    data() {
        return {
            address: "https://collectionapi.metmuseum.org",
            endpoints: {
                search: "/public/collection/v1/search",
                object: "/public/collection/v1/objects",
            },
            keyWords: [
                "Nationalism",
                "Classicism",
                "Baroque",
                "Realism",
                "Impressionism"
            ],
            artList: new Array,
            currentArt: {
                title: "",
                artist: "",
                image: ""
            }
        }
    },


    async mounted() {
        // Add all art to artList
        this.artList = await this.searchMuseum(this.keyWords);
        // Randomize the current art
        await this.randomizeCurrentArt();
    },

    methods: {
        // Randomize the art on screen
        async randomizeCurrentArt() {
            // Reset the current art
            this.currentArt = {
                title: "",
                artist: "",
                image: ""
            }

            // Get a random art from the list making sure it has an image and set it as the current art
            do {
                const randomArtFromArray = this.artList[Math.floor(Math.random() * this.artList.length >> 0)];
                this.currentArt = await this.getArtById(randomArtFromArray);
            } while (this.currentArt.image === "");
        },

        // Search the API for art matching the keywords
        async searchMuseum(query: string | string[]): Promise<Number[]> {

            console.log("Searching for art matching: " + query);

            let artIds: Number[] = [];

            // If the query is a string, convert it to an array
            if (typeof query === "string") {
                query = [query];
            }
            
            // Loop through the keywords and search the API for art matching each keyword
            for (let i = 0; i < query.length; i++) {
                const response = await fetch(this.address + this.endpoints.search + "?medium=Paintings&hasImages=true&isHighlight=true&" + "q=" + query[i]);
                const data = await response.json();
                artIds.push(...data.objectIDs);
            }
            

            console.log("Found " + artIds.length + " results");
            
            return artIds;
        },

        // Get the specific art piece by it's ID
        async getArtById(id: Number): Promise<{title: string, artist: string, image: string}> {
            const response = await fetch(this.address + this.endpoints.object + "/" + id);
            const data = await response.json();

            return { title: data.title, artist: data.artistDisplayName, image: data.primaryImage };
        }
    }
});
</script>