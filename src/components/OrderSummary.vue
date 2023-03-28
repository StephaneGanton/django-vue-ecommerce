<template>
  <div class="box mb-4">
    <div class="is-size-4 mb-6">Order #{{ order.id }}</div>

    <h4 class="is-size-5">Produits</h4>

    <table class="table is-fullwidth">
      <thead>
        <tr>
          <th>Product</th>
          <th class="has-text-centered">Price</th>
          <th class="has-text-centered">Quantity</th>
          <th class="has-text-centered">Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in order.items" :key="index">
          <td>{{ item.product.name }}</td>
          <td class="has-text-centered">${{ item.product.price }}</td>
          <td class="has-text-centered">{{ item.quantity }}</td>
          <td class="has-text-centered">
            ${{ getItemTotal(item).toFixed(2) }}
          </td>
        </tr>
      </tbody>
      <tfoot>
        <!--tr>
          <td colspan="2">Total</td>
          <td class="has-text-centered">{{ cartTotalLength }}</td>
          <td class="has-text-centered">
            <strong>${{ cartTotalPrice.toFixed(2) }}</strong>
          </td>
        </tr-->
      </tfoot>
    </table>
  </div>
</template>

<script>
export default {
  name: "OrderSummary",

  props: {
      order: Object,
  },

  methods:{
      getItemTotal(item){
          return item.quantity * item.product.price
      },

       orderTotalLength(){
            return order.items.reduce((acc, curVal)=>{
                return acc += curVal.quantity
            }, 0)
        },

        /*cartTotalPrice(){
            return this.cart.items.reduce((acc, curVal)=>{
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        }*/
  }
};
</script>