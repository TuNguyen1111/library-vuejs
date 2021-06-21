<template>
    <div class="flip-card">
        <div class="flip-card-inner">
            <div class="flip-card-front">
                <v-img
                    :src="book.img_src"
                    width="150"
                    height="198"
                    class="book-img"
                >
                </v-img>
            </div>
            <div class="flip-card-back">
                <h3>{{ book.name }}</h3>
                <h3>{{ book.author }}</h3>
                <h3>{{ book.price }} vnd</h3>
                <v-btn
                    elevation="2"
                    @click="addToCart()"
                >Buy</v-btn>
            </div>
        </div>
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
.flip-card {
  background-color: transparent;
  width: 300px;
  height: 200px;
  perspective: 1000px; 
  margin-left: 3%;
}

.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.8s;
  transform-style: preserve-3d;
}

.flip-card:hover .flip-card-inner {
  transform: rotateY(180deg);
}

.flip-card-front, .flip-card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.flip-card-back {
  background-color: white;
  color: black;
  transform: rotateY(180deg);
}

.book-img {
    border: 4px solid white;
}

</style>