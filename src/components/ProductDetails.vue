<template>
  <section v-if="product">
    <h2>Product Details</h2>
    <h3>{{ product.name }}</h3>
    <Suspence>
      <template #fallback>
        <p>loading ...</p>
      </template>
      <template #default>
        <p data-testid="product-count">
          {{ n(productsInCartForId(product.id).quantity, 'currency') }}
        </p>
      </template>
    </Suspence>
  </section>
</template>

<script>
import { useStore } from 'vuex'
import { useI18n } from 'vue-i18n'

export default {
  props: {
    productId: { required: true, type: [Number, String] }
  },
  async setup(props) {
    const { n } = useI18n()
    const { dispatch, getters } = useStore()
    const product = await dispatch('products/getProduct', props.productId)
    return {
      n,
      product,
      productsInCartForId: getters['cart/productsInCartForId']
    }
  }
}
</script>

<style scoped>
section {
  text-align: left;
}
</style>
