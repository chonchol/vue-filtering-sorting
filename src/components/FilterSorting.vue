<script>
import axios from "axios";
export default {
  created() {},
  methods: {
    async getProduct() {
      const data = await axios.get("http://localhost:3000/products");
      console.log(data);
    },
  },
  beforeMount() {
    this.getProduct();
  },
};
</script>

<template>
  <div class="greetings">Hello world</div>
  <div class="content-area">
    <div class="left-sidebar">
      <div class="filter-group ws-box show" data-group-type="status">
        <div class="label">
          <span>Availability</span>
        </div>
        <div class="items">
          <label class="filter">
            <input type="checkbox" name="status" value="7" />
            <span>In Stock</span>
          </label>
          <label class="filter">
            <input type="checkbox" name="status" value="8" />
            <span>Pre Order</span>
          </label>
          <label class="filter">
            <input type="checkbox" name="status" value="9" />
            <span>Up Coming</span>
          </label>
        </div>
      </div>

      <div class="filter-group ws-box show" data-group-id="211">
        <div class="label">
          <span>Brand</span>
        </div>
        <div class="items">
          <label class="filter">
            <input type="checkbox" name="filter" value="1058" />
            <span>Razer</span>
          </label>
          <label class="filter">
            <input type="checkbox" name="filter" value="1061" />
            <span>Apple</span>
          </label>
          <label class="filter">
            <input type="checkbox" name="filter" value="1062" />
            <span>Microsoft</span>
          </label>
        </div>
      </div>
    </div>
    <div class="right-area">
      <div class="top-bar ws-box">
        <div class="row">
          <div class="col-sm-4 col-xs-2 actions">
            <button class="tool-btn" id="lc-toggle">
              <i class="material-icons">filter_list</i> Filter
            </button>
            <label class="page-heading m-hide">All Laptop</label>
          </div>
          <div class="col-sm-8 col-xs-10 show-sort">
            <div class="form-group rs-none">
              <label>Show:</label>
              <div class="custom-select">
                <select id="input-limit">
                  <option value="20" selected="selected">20</option>
                  <option value="24">24</option>
                  <option value="48">48</option>
                  <option value="75">75</option>
                  <option value="90">90</option>
                </select>
              </div>
            </div>
            <div class="form-group">
              <label>Sort By:</label>
              <div class="custom-select">
                <select id="input-sort">
                  <option value="">Default</option>
                  <option value="p.price-ASC">Price (Low &gt; High)</option>
                  <option value="p.price-DESC">Price (High &gt; Low)</option>
                </select>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.content-area {
  display: flex;
  justify-content: center;
  width: 100%;
}
.left-sidebar{
  width: 20%;
}
.right-area{
  width: 80%;
}
</style>
