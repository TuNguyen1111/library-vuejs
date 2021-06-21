<template>
    <div class="">
        <h2 id="title">Top rated book</h2>
        <div class="wrapper">
            
            <div class="top-rated-books" id="rated-books">
                <Book :key="book.id" v-for="book in topRatedBooks" :book="book"/>
            </div>
            
        </div>
    </div>
</template>
<script lang="ts">
import Vue from 'vue'
import Book from '@/components/Book.vue'

export default Vue.extend({
    name: "TopRatedBooks",
    components: {
        Book
    },

    data: () => ({
        topRatedBooks: [],
        apiLinks: "http://localhost:3000/top_rated_books"
    }),

    async created() {
        this.topRatedBooks = await this.getTopRatedBooks()
        this.slideImages()
    },

    methods: {
        slideImages() {
            let arrowLeft = document.getElementById('arrow-left')
            let arrowRight = document.getElementById('arrow-right')
            let topRatedBooks = document.getElementById('rated-books')
            console.log(topRatedBooks)

            arrowLeft?.addEventListener('click', function() {
               if (topRatedBooks) {
                   topRatedBooks.scrollLeft -= 180
               }
               
            })

            arrowRight?.addEventListener('click', function() {
                if (topRatedBooks) {
                   topRatedBooks.scrollLeft += 180
               }
            })
        },

        async getTopRatedBooks() {
            let booksRespone = await fetch(this.apiLinks)
            let topRatedBooks = await booksRespone.json()
            return topRatedBooks
        }
    }

})
</script>

<style scoped>
.wrapper {
    display: flex;
    align-items: center;    
    justify-content: space-evenly
}

.top-rated-books {
    width: 100%;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    /* overflow-x: hidden; */
}

#title {
    text-align: left;
    color: rgb(243, 33, 33);
}

.arrow{
	width: 30px;
	height: 30px;
	cursor: pointer;
	transition: .3s;
}

.arrow:hover{
	opacity: .5;
	width: 35px;
	height: 35px;
}
</style>