<template>
  <div style="background-color:black">
    <div class="container text-white">
      <div class="row">
        <div class="col-md-3">
          <h6 class="my-4">
            <router-link class="text-vrpink" to="/products">
              <i class="fas fa-arrow-left"></i> 返回2
            </router-link>
          </h6>
        </div>
        <div class="col-md-9 my-4">
          <div class="row">
            <div class="col-md-8">
              <img :src="product.image" alt style class="img-fluid rounded border" />
            </div>
            <div class="col-md-4 border border-vrblue rounded py-3 px-2">
              <h4>{{product.title}}</h4>
              <hr class="border-bottom-0 border-vrpink" />
              <div class="d-flex justify-content-around">
                <p class="line-through">原價: {{product.origin_price | currency}}</p>
                <p class="text-danger font-weight-bold">特價: {{product.price | currency}}</p>
              </div>
              <div class="border rounded border-vrpink p-2">
                <p class="m-0">產品介紹:</p>
                <p>{{product.description}}</p>
              </div>
              <div class="my-3">
                <span>加購數量</span>
                <select name id>
                  <option :value="number" v-for="number in 10" :key="number">{{number}}</option>
                </select>
              </div>

              <div class="d-flex justify-content-between my-3">
                <button class="btn btn-vrpink">ADD TO BAG</button>
                <button class="btn btn-vrpink">BUY NOW</button>
              </div>
            </div>
          </div>
          <p class="border border-vrpink rounded p-2 my-2">{{product.content}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      id: "",
      product: {}
    };
  },
  methods: {
    getProduct() {
      const vm = this;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/product/${vm.id}`;
      this.$http.get(api).then(response => {
        vm.product = response.data.product;
        console.log(response.data);
        console.log(vm.product);
        console.log(response.data.product);
      });
    }
  },
  created() {
    this.id = this.$route.params.id;
    this.getProduct();
  }
};
</script>