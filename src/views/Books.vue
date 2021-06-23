<template>
    <div class="books">
        <div class="search">
            <v-text-field
                label="Search"
                hide-details="auto"
                :dark="true"
                v-model="searchKey"
                @keyup="searchBook()"
            ></v-text-field>
            <div class="search-result" id="search-result">

            </div>
        </div>
        <div v-if="!haveSearchResult">
            <div id="books-row" v-for="(booksRow, index) in books" :key="index">
                <div class="books-row">
                    <Book v-for="book in booksRow" :key="book.id" :book="book"/>
                </div>
                <div class="bookshelf"></div>
            </div>
        </div>
        <div v-else>
            <div class="books-search" id="books-search">
                <Book v-for="book in booksSearchResult" :key="book.id" :book="book"/>
            </div>
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
        booksApi: "http://localhost:3000/books",
        searchKey: '',
        booksSearchResult: [],
        haveSearchResult: false
    }),

    async created() {
        this.books = await this.getBooksFromApi()
    },

    methods: {
        async getBooksFromApi() {
            let booksRespone = await fetch(this.booksApi)
            let books = await booksRespone.json()
            return books
        },
        searchBook(){
            let searchResult = document.getElementById('search-result')
            if (searchResult) {
                searchResult.innerHTML = ''
            }
            this.booksSearchResult = []
            
            if (!this.searchKey.length) {
                if (searchResult) {
                    searchResult.style.display = 'none'
                    this.haveSearchResult = false
                }
            } else {
                if (searchResult) {
                    searchResult.style.display = 'block'
                }

                for (const [row, booksOfRow] of Object.entries(this.books)) {
                    console.log(row)
                    for (let book of booksOfRow) {
                        let bookName = book.name
                        if (bookName.includes(this.searchKey)){
                            let h3 = document.createElement("h3")
                            h3.innerHTML = bookName
                            searchResult?.appendChild(h3)
                            
                            let isBookInBooksSearch = this.isBookInBooksSearch(book, this.booksSearchResult)
                            if (!isBookInBooksSearch) {
                                this.booksSearchResult.push(book)
                            }
                            if (this.booksSearchResult) {
                                this.haveSearchResult = true
                            }
                        }
                    }
                }
            }
        },

        isBookInBooksSearch(book: object, booksSearchResult: Array<object>) {
            for (let bookSearch of booksSearchResult) {
                if (book.id === bookSearch.id) {
                    return true
                }
            }
            return false
        }
    }
})
</script>

<style scoped>
.books-row,
.books-search {
    display: flex;
    align-items: center;
    justify-content: space-around;
    margin-top: 3%;
    text-align: center;
}

.book-search {
    display: none;
}

.search {
    width: 70%;
    margin: auto;
}

.search-result {
    text-align: left;
    background: white;
    display: none;
    opacity: 0.5;
}

.bookshelf {
  height: 15px;
  background: orange;
}
</style>