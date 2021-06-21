<template>
    <div class="books">
        <div class="book-row" v-for="(booksRow, index) in books" :key="index">
            <Book v-for="book in booksRow" :key="book.id" :book="book"/>
        </div>
    </div>
</template>

<script lang="ts">
import Vue from 'vue'
import Book from '@/components/Book.vue'
export default Vue.extend({
    name: "Books",
    components: {
        Book
    },

    data: () => ({
        books: [],
        booksApi: "http://localhost:3000/books"
    }),

    async created() {
        this.books = await this.getBooksFromApi()
    },

    methods: {
        async getBooksFromApi() {
            let booksRespone = await fetch(this.booksApi)
            let books = await booksRespone.json()
            return books
        }
    }
})
</script>

<style scoped>
.book-row {
    display: flex;
    align-items: center;
    justify-content: space-around;
    margin-bottom: 3%;
    text-align: center;
}
</style>