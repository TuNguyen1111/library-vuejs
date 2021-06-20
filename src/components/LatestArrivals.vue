<template>
    <div>
        <h2 id="title">Latest Arrivals</h2>
        <div class="latest-arrival">
            <Book :key="book.id" v-for="book in latesArrivals" :book="book"/>
        </div>
    </div>
</template>
<script lang="ts">
import Vue from 'vue'
import Book from '@/components/Book.vue'

export default Vue.extend({
    name: "LatesArrivals",
    components: {
        Book
    },

    data: () => ({
        latesArrivals: [],
        apiLinks: "http://localhost:3000/latest_arrival"
    }),

    async created() {
        this.latesArrivals = await this.getlatesArrivals()
        console.log(this.latesArrivals)
    },

    methods: {
        async getlatesArrivals() {
            let booksRespone = await fetch(this.apiLinks)
            let latesArrivals = await booksRespone.json()
            return latesArrivals
        }
    }

})
</script>

<style scoped>
.latest-arrival {
    display: flex;
    justify-content: space-evenly;
}

#title {
    text-align: left;
    color: rgb(243, 33, 33);
}
</style>