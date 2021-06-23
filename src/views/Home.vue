<template>
  <div class="home">
    <div class="left-size">
      <TopRatedBooks />
      <div class="bookshelf"></div>
      <LatestArrivals />
      <div class="bookshelf"></div>
    </div>

    <div class="right-size">
      <v-card
        elevation="24"
        max-width="444"
        class="mx-auto"
      >
        <v-system-bar lights-out></v-system-bar>
        <v-carousel
          continuous
          :cycle="cycle"
          :show-arrows="false"
          hide-delimiter-background
          delimiter-icon="mdi-minus"
          height="300"
        >
          <v-carousel-item
            v-for="(book, i) in booksOfAuthor"
            :key="i"
          >
            <v-sheet
              height="100%"
              tile
            >
              <v-row
                class="fill-height"
                align="center"
                justify="center"
              >
                <div class="text-h2">
                  <v-img :src="book.img_src" height="300" contain>
                  </v-img>
                </div>
              </v-row>
            </v-sheet>
          </v-carousel-item>
        </v-carousel>
        <v-list two-line>
          <v-list-item>
            <v-list-item-avatar>
              <v-img :src="author.img"></v-img>
            </v-list-item-avatar>
            <v-list-item-content>
              <v-list-item-title>{{ author['name'] }}</v-list-item-title>
              <v-list-item-subtitle>Author</v-list-item-subtitle>
            </v-list-item-content>
            <v-list-item-action>
              <v-switch
                v-model="cycle"
                label="Cycle Slides"
                inset
              ></v-switch>
            </v-list-item-action>
          </v-list-item>
        </v-list>
      </v-card>
    </div>
    
  </div>
</template>

<script>
// @ is an alias to /src
import TopRatedBooks from '@/components/TopRatedBooks.vue'
import LatestArrivals from '@/components/LatestArrivals.vue'

export default {
  name: 'Home',
  components: {
    TopRatedBooks,
    LatestArrivals,
  },
  data: () => ({
    books: [],
    booksApi: "http://localhost:3000/books",
    authorsAndBooks: {},
    author: {},
    booksOfAuthor: [],
    cycle: true
  }),

  async created() {
    this.books = await this.getBooksFromApi()

    for (const [row, booksOfRow] of Object.entries(this.books)) {
      console.log(row)
      for (let book of booksOfRow) {
        let bookAuthor = book.author
        let bookList = []

        if (bookAuthor in this.authorsAndBooks) {
          bookList = this.authorsAndBooks[bookAuthor]
        } else {
          this.authorsAndBooks[bookAuthor] = bookList
        }
        bookList.push(book)
      }
    }

    let authorName = Object.keys(this.authorsAndBooks)[0]
    this.booksOfAuthor = this.authorsAndBooks[authorName]
    this.author['name'] = authorName
    this.author['img'] = this.booksOfAuthor[0]['author_img']
  },

  methods: {
    async getBooksFromApi() {
      let booksRespone = await fetch(this.booksApi)
      let books = await booksRespone.json()
      return books
    },
  }
}
</script>
<style scoped>
.home {
  display: flex;
}

.left-size {
  width: 60%;
}

.right-size {
  width: 40%;
}

.bookshelf {
  height: 15px;
  background: orange;
}
</style>
