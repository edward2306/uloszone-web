<template>
  <div class="col-md-12">
    <div class="card globalcard">
      <div class="card-header">
        <h5 style="float: left"> {{ title }}</h5>
        <h6 style="float: right">
          <a href="/search" style="color: -webkit-link">Lihat Semua Produk</a>
        </h6>
      </div>
      <div class="card-body globalcardbody">
        <carousel
          :mouse-drag="true"
          :scrollPerPage="true"
          :spacePadding="20"
          :speed="800"
          :paginationEnabled="false"
          :perPageCustom="[[0, 1], [991.88, 6]]"
        >
          <slide class="px-2" v-for="product in products">
            <a :href="'/products/' + product.id">
            <div class="card product ">

                <div class="imgwrapper">
                  <img :src="'/images/' + JSON.parse(product.images)[0]" alt="Card image cap">
                </div>


              <div class="card-body">
                <p class="card-title productname">{{product.name}}</p>
                <h6 style="color: #ff5205">Rp {{formatPrice (product.price)}}</h6>
                <p class="card-text float-right">
                  <small class="text-muted">{{ product.merchant.profile.name }}</small>
                </p>

              </div>
            </div>
            </a>
          </slide>
        </carousel>
      </div>
    </div>
  </div>

</template>

<script>
import carousel2 from "vue-owl-carousel";
import { Carousel, Slide } from "vue-carousel";
import EventBus from "../../eventBus";

export default {
  components: { carousel2, Carousel, Slide },
  props: ["userId", "title"],
  data() {
    return {
      products: []
    };
  },
  methods: {
      formatPrice(value) {
          let val = (value/1).toFixed().replace('.', ',')
          return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
      },
    async getAllProducts() {
      await window.axios
        .get("/api/products")
        .then(res => {
          this.products = res.data;
        })
        .catch(err => {
          console.log(err);
        });
    },
    async addToCart(id) {
      let payload = {
        productId: id,
        total: 1,
        userId: this.userId
      };
    
      await window.axios
        .post("/api/carts", payload)
        .then(res => {
          this.emitEvent(res.data);
        })
        .catch(err => {
          console.log(err);
        });
    },
    emitEvent(data) {
      EventBus.$emit("CART_UPDATED", data);
    }
  },
  mounted() {
    this.getAllProducts();
  }
};
</script>
