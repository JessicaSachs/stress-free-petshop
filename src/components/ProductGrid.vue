<template>
  <section class="container" v-bind="$attrs">
    <ProductCard
        @click="goToProduct(product.id)"
        v-for="product in products"
        :key="product.id">
      {{ product.name }}
      <template #footer>
        <Stepper v-model:value="product.quantity"/>
        <p>{{ totalForQuantity(product) }}</p>
      </template>
    </ProductCard>
  </section>
  <Suspense>
    <router-view :key="$route.params.productId" :productId="$route.params.productId"/>
  </Suspense>
</template>

<script>
import { useI18n } from 'vue-i18n'
import { useStore, createNamespacedHelpers } from 'vuex'
import ProductCard from '@/components/ProductCard'
import Stepper from './Stepper'

const { mapState } = createNamespacedHelpers('products')
export default {
  components: { ProductCard, Stepper },
  methods: {
    goToProduct(id) {
      this.$router.push(`/${id}`)
    }
  },
  computed: {
    ...mapState(['products']),
    totalForQuantity() {
      return ({ price, quantity }) => {
        const displayPrice = quantity === 0 ? price : price * quantity
        return this.n(displayPrice, 'currency')
      }
    }
  },
  setup() {
    const { n } = useI18n()
    const { dispatch } = useStore()
    dispatch('products/fetchProducts')
    return { n }
  }
}
</script>

<style lang="scss" scoped>
  .container {
    margin: 0 auto;
    display: grid;
    grid-gap: 2rem;
    grid-template-columns: repeat(auto-fill, minmax(230px, 1fr) );
  }
</style>
