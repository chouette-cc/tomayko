<template>
  <div class="shopPage">
    <Nav :title="category[0].name" />
    <swiper ref="mySwiper" class="filters" :options="$device.isMobile ? swiperOptions : swiperOptionsDesktop">
          <swiper-slide class="filter" >
              <span>T-Shirt</span>
          </swiper-slide>
          <swiper-slide class="filter" >
              <span >Sweats</span>
          </swiper-slide>
          <swiper-slide class="filter" >
              <span >Pants</span>
          </swiper-slide>
          <swiper-slide class="filter" >
              <span >Shooes</span>
          </swiper-slide>
          <swiper-slide class="filter" >
              <span >Hats</span>
          </swiper-slide>
    </swiper>
    <ul class="shopPage_nav2">
      <li>
        <img :src="require('@/assets/icons/filters.svg')" alt="Filter icon">
        <span>Filters</span>
      </li>
      <li class="pointer" @click="orderedByPrice = !orderedByPrice">
        <img :src="require('@/assets/icons/arrows.svg')" alt="Filter icon">
        <span>Price: {{ orderedByPrice ? 'default' : 'lowest to high' }}</span>
      </li>
      <li @click="displayHorizontal = !displayHorizontal">
        <img :src="require(displayHorizontal ? '@/assets/icons/grid.svg' : '@/assets/icons/grid_2.svg')" alt="Filter icon">
      </li>
    </ul>
    <div v-if="category[0].products && !orderedByPrice" class="products">
      <Product v-for="(product, index) in category[0].products" :key="index" :data="product" :display-horizontal="displayHorizontal"/>
    </div>
    <div v-if="category[0].products && orderedByPrice" class="products">
      <Product v-for="(product, index) in orderByPrice" :key="index" :data="product" :display-horizontal="displayHorizontal"/>
    </div>
    <p v-if="category[0].products.length == 0" class="noProducts">No products</p>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import _ from 'lodash'

export default {
  transition: 'home',
  async asyncData({ $strapi, store, error }) {
    try {
      // const response = await $strapi.$products.find('category.name='+category)
      const response = await $strapi.$categories.find([['name', store.state.categoryActuel]])
      store.commit('setCategory', response)
    } catch (e) {
      error(e)
    }
  },
  data(){
    return{
      swiperOptions: {
          slidesPerView: 3.6,
          spaceBetween: 0,
          pagination: {
            el: '.swiper-pagination',
            clickable: true
          },
          // Some Swiper option/callback...
        },
      swiperOptionsDesktop: {
        slidesPerView: 6,
        pagination: {
          el: '.swiper-pagination',
          clickable: true
        },
        // Some Swiper option/callback...
      },
      displayHorizontal: false,
      orderedByPrice: false
    }
  },
  computed: {
      ...mapState(['category']),
      orderByPrice () {
        return _.orderBy(this.category[0].products, 'price')
      }
  },
  methods: {
      changePage(url) {
          this.$router.push({ path: url })
      }
  }
}
</script>

<style lang="scss" scoped>
.shopPage{
    @include tablet-portrait-up {    
        width: 70%;
        margin: auto;
    };
    .noProducts{
      text-align: center;
      margin: 32px;
    }
    &_nav{
      display: flex;
      justify-content: space-between;
      align-items: center;
      background-color: $black;
      padding: 16px;
      .chevron{
        cursor: pointer;
      }
    }
    &_nav2{
      display: flex;
      justify-content: space-around;
    }
    .filters{
      margin: 8px;
      .filter{
        padding: 8px;
        margin: 8px;
        background-color: white;
        border-radius: 32px;
        text-align: center;
        font-weight: 600;
        cursor: pointer;
        color: $black;
      }
    }
    .products{
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
      
    }
    
}
</style>
