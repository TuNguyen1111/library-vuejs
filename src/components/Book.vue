<template>
    <div>
        <v-img
            :src="book.img_src"
            width="200"
            height="300"
            class="book-img"
            @click="addToCart()"
        >

        </v-img>
    </div>
</template>
<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    name: "Book",
    props: {
        book: Object,
    },
    data: () => ({
        cartApi: "http://localhost:3000/cart",
        cart: [],
        bookOrderd: {}
    }),

    async created() {
        this.cart = await this.getCart()
    },

    methods: {
        isBookInCart() {
            for (let book of this.cart) {
                console.log(book.id, this.book.id)
                if (book.id === this.book.id) {
                    this.bookOrderd = book
                    return true
                }
            }
            this.bookOrderd = this.book
            this.bookOrderd.quantity = 1
            this.bookOrderd.price = 120000
            return false
        },

        async addToCart() {
            let bookInCart = this.isBookInCart()
            console.log(bookInCart)
            if (!bookInCart) {
                console.log('ININ')
                let bookRespone = await fetch(this.cartApi, {
                    method: "POST",
                    headers: {
                        "Content-type": "application/json"
                    },
                    body: JSON.stringify(this.bookOrderd)
                })
                let book = await bookRespone.json()
                this.cart.push(book)
            } else {
                this.bookOrderd.quantity += 1
                let bookUpdated = await this.updateBookQuantity(this.bookOrderd)
                this.bookOrderd = bookUpdated
            }
        },

        async updateBookQuantity(book: object) {
            let bookRespone = await fetch(this.cartApi + "/" + this.book.id, {
                method: "PUT",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(book)
            })

            let bookUpdated = await bookRespone.json()
            return bookUpdated
        },

        async getCart() {
            let cartRespone = await fetch(this.cartApi)
            let cart = await cartRespone.json()
            return cart
        }
    },
})
</script>

<style scoped>
.book-img {
    border: 4px solid white;
    transition: all 0.4s ease-in;
}

.book-img:hover {
    transform: scale(120%);
}
</style>