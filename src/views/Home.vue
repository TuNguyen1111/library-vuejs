<template>
  <div class="home">
    <div class="left-size">
      <TopRatedBooks />
      <div class="bookshelf"></div>
      <LatestArrivals />
      <div class="bookshelf"></div>
    </div>

    <div class="right-size">
       <div>
        <v-text-field
          label="Search"
          hide-details="auto"
          :dark="true"
        ></v-text-field>
      </div>
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
    LatestArrivals
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
