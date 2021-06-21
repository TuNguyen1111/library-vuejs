<template>
    <div class="home">
        <div class="books-list">
            <v-simple-table>
                <template v-slot:default>
                    <thead>
                        <tr>
                            <th></th>
                            <th class="text-center">
                                Name
                            </th>
                            <th class="text-center">
                                Author
                            </th>
                            <th class="text-center">
                                Quantity
                            </th>
                            <th class="text-center">
                                Price
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr
                            v-for="book in cart"
                            :key="book.id"
                        >   
                            <td></td>
                            <td>{{ book.name }}</td>
                            <td>{{ book.author }}</td>
                            <td class="quantity">
                                <div 
                                    id="minus" 
                                    @click="decreaseOrIncreaseQuantity(book, book.id, decrease=true)"
                                >
                                    &minus;
                                </div>
                                {{ book.quantity }}
                                <div 
                                    id="plus" 
                                    @click="decreaseOrIncreaseQuantity(book, book.id, decrease=false)"
                                >
                                    &plus;
                                </div>
                            </td>
                            <td>{{ book.quantity * book.price }} vnd</td>
                        </tr>
                        <tr>
                            <td>Total:</td>
                            <td></td>
                            <td></td>
                            <td></td>
                            <td>{{ totalPrice }} vnd</td>
                        </tr>
                    </tbody>
                </template>
            </v-simple-table>
        </div>
        
        <div class="overview">
            <v-card
                v-for="book in cart"
                :key="book.id"
                class="book-img mx-auto"
                max-width="400"
            >
                <v-img
                    class="white--text align-end"
                    height="200px"
                    :src="book.img_src"
                >
                    <v-card-title>Top 10 Australian beaches</v-card-title>
                </v-img>

                <v-card-subtitle class="pb-0">
                    Number 10
                </v-card-subtitle>

                <v-card-text class="text--primary">
                    <div>Whitehaven Beach</div>

                    <div>Whitsunday Island, Whitsunday Islands</div>
                </v-card-text>

                <v-card-actions>
                    <v-btn
                        color="orange"
                        text
                    >
                        Share
                    </v-btn>

                    <v-btn
                        color="orange"
                        text
                    >
                        Explore
                    </v-btn>
                </v-card-actions>
            </v-card>
        </div>

    </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
    name: "Cart",
    components: {
        
    },
    data: () => ({
        cartApi: "http://localhost:3000/cart",
        cart: [],
        totalPrice: 0,
    }),
    async created() {
        let cartRespone = await this.getCart()
        for (let book of cartRespone) {
            if (book.quantity > 0) {
                this.cart.push(book)
            }
        }
        this.totalPrice = this.updateTotalPrice()
    },
    methods: {
        updateTotalPrice() {
            let totalPrice = 0
            for (let book of this.cart) {
                totalPrice += book.price * book.quantity
            }
            return totalPrice
        },

        async getCart() {
            let cartRespone = await fetch(this.cartApi)
            let cart = await cartRespone.json()
            return cart
        },

        async decreaseOrIncreaseQuantity(book: object, bookId: number, decrease=false) {
            if (decrease) {
                book.quantity -= 1
            } else {
                book.quantity += 1
            }
            
            let bookUpdated = await this.updateBookQuantity(book, bookId)
            this.cart = this.cart.filter(book => book.id === bookUpdated.id ? book.quantity = bookUpdated.quantity : book )
            this.totalPrice = this.updateTotalPrice()
        },

        async updateBookQuantity(book: object, bookId: number) {
            let bookRespone = await fetch(this.cartApi + "/" + bookId, {
                method: "PUT",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(book)
            })

            let bookUpdated = await bookRespone.json()
            return bookUpdated
        },
    }
})
</script>

<style scoped>

.home {
    display: flex;
    justify-content: space-around;
}

.quantity {
    display: flex;
    align-items: center;
    text-align: center;
    justify-content: space-evenly
}

#minus, #plus {
    cursor: pointer;
}

.books-list {
    width: 40%;
}

.overview {
    width: 50%;
    display: flex;
    flex-wrap: wrap;
}

.book-img {
    margin-bottom: 10px;
}
</style>