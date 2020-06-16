<template>
  <div class="products-banner">
    <loading :active.sync="isLoading"></loading>
    <div class="container">
      <div class="row py-6">
        <div class="col-lg-4">
          <h2 class="text-center py-3 text-vrpink">產品列表</h2>

          <!--<ul class="list-group d-flex flex-column align-items-center catrgory">
            <li
              class="list-group-item border-vrpink border d-flex align-items-center rounded w-50 justify-content-around py-2 my-2 px-2"
              v-for="(item,key) in filterlist.list"
              :key="key"
            >
              <i class="fas fa-vr-cardboard"></i>
              <a href @click.prevent="filterlists(item)">VR {{item}}</a>
            </li>
          </ul>-->
          <div class="list-group justify-content-center align-items-center text-center">
            <a
              href="#"
              class="list-group-item list-group-item-action w-50 rounded border-vrpink border my-2"
              v-for="(item,key) in filterlist.list"
              :key="key"
              @click.prevent="filterlists(item)"
            >
              <i class="fas fa-vr-cardboard"></i>
              VR {{item}}
            </a>
          </div>
        </div>
        <div class="col-lg-8">
          <div class="row justify-content-around flex-wrap align-items-center">
            <div
              class="card col-md-3 col-sm-5 col-8 mx-3 my-2 products bg-dark"
              v-for="(item , key) in products"
              :key="key"
            >
              <div class="d-flex justify-content-center flex-column align-items-center">
                <div class="show-more my-2">
                  <img
                    :src="item.image"
                    alt
                    class="img-fluid rounded"
                    style="height:100px ;width:158px;"
                  />
                  <div class="show-info">
                    <button class="btn btn-vrblue text-white" @click="getProduct(item.id)">了解詳情</button>
                  </div>
                </div>

                <div class="card-body d-flex flex-column align-items-center text-white">
                  <h4>{{item.title}}</h4>

                  <select name id class="rounded border border-vrpink my-2">
                    <option :value="number" v-for="(number,key) in 10" :key="key">{{number}}</option>
                  </select>
                  <p>{{item.price | currency}}</p>
                  <button
                    type="button"
                    class="btn btn-outline-vrpink"
                    @click="addCart(item.id,item.number)"
                  >加入購物車</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      products: {},
      product: {},
      filterlist: {
        list: ["全部商品", "頭顯", "遊戲", "配備"],
        str: "全部商品"
      },
      list: {},

      index: "",
      isLoading: false
    };
  },

  methods: {
    getProducts() {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/products/all`;
      const vm = this;
      vm.isLoading = true;
      this.$http.get(api).then(response => {
        vm.isLoading = false;
        vm.pagination = response.data.pagination;
        if (vm.filterlist.str !== "全部商品") {
          let filterProducts = response.data.products.filter((item, i) => {
            return item.category === vm.filterlist.str;
          });
          vm.products = filterProducts;
          console.log(vm.products);
        } else {
          vm.products = response.data.products;
        }
        console.log(response.data);
        console.log("pr", vm.products);
      });
    },
    getProduct(id) {
      console.log("123", id);
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/product/${id}`;
      const vm = this;
      this.$http.get(api).then((response)=>{
        console.log(response)
        this.$router.push(`/product/${response.data.product.id}`)
      });
    },
    filterlists(str) {
      //取得列表
      const vm = this;
      vm.filterlist.str = str;
      vm.getProducts();
    },
    addCart(id, qty = 1) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
      const vm = this;
      const cart = {
        product_id: id,
        qty
      };
      vm.isLoading = true;
      this.$http.post(api, { data: cart }).then(response => {
        console.log(response);
        vm.isLoading = false;
        this.$bus.$emit("messsage:push", response.data.message, "success");
        this.getCartVal();
      });
    },
    getCartVal() {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
      const vm = this;
      this.$http.get(api).then(response => {
        console.log(response.data);
        this.$bus.$emit("get:cartval", response.data.data.carts.length);
      });
    }
  },

  created() {
    this.getProducts();
    this.getCartVal();
  }
};
</script>

<style>
.catrgory li {
  cursor: pointer;
}
.catrgory li a,
.catrgory li i {
  color: white;
}
.catrgory li:hover a,
.catrgory li:hover i {
  color: #09cfff;
  display: block;
}

.list-group-item:hover {
  color: #09cfff;
  background-color: black;
  text-decoration: underline;
}
.show-more {
  position: relative;
}
.show-info {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0);
  transition: all 0.3s;
  text-align: center;
  display: none;
}
.show-info button {
  font-size: 12px;
  width: 50%;
}
.show-more:hover .show-info {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  background-color: rgba(80, 78, 78, 0.5);
}

.products {
  position: relative;
}
.products-banner {
  background: url(../assets/productbanner.jpg);
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
}
</style>