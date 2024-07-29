<script>
import axios from "axios";
export default {
  data() {
    return {
      products: [],
      selectedBrands: [],
      selectedStatuses: [],
      selectedCategories: [],
      sortOption: "",
      productsPerPage: 10,  
      currentPage: 1,
      pageOptions: [10, 20, 30],  
      priceFrom: 0,  
      priceTo: Infinity,  
    };
  },
  methods: {
    async getProduct() {
      const response = await axios.get("http://localhost:3000/products");
      this.products = response.data;

      console.log(this.products);
    },
  },

  watch: {
    productsPerPage() {
      this.currentPage = 1;  
      console.log(this.currentPage)
    },
    priceFrom() {
      this.currentPage = 1;  
    },
    priceTo() {
      this.currentPage = 1;  
    }
  },

  computed: {
    availableProductStatus() {
      return [
        ...new Set(this.products.map((product) => product.availabilityStatus)),
      ];
    },

    availableCategory() {
      return [...new Set(this.products.map((product) => product.category))];
    },

    availableBrands() {
      return [...new Set(this.products.map((product) => product.brand))];
    },

    filteredProducts() {

      let filtered = this.products.filter((product) => {
        const statusMatch =
          this.selectedStatuses.length === 0 ||
          this.selectedStatuses.includes(product.availabilityStatus);

        const priceMatch = product.price >= this.priceFrom && product.price <= this.priceTo;

        const categoryMatch =
          this.selectedCategories.length === 0 ||
          this.selectedCategories.includes(product.category);

        const brandMatch =
          this.selectedBrands.length === 0 ||
          this.selectedBrands.includes(product.brand);
        return statusMatch && priceMatch && categoryMatch && brandMatch;
      });

      if (this.sortOption === "highToLow") {
        filtered.sort((a, b) => b.price - a.price);
      } else if (this.sortOption === "lowToHigh") {
        filtered.sort((a, b) => a.price - b.price);
      }

      return filtered;
    },

    paginatedProducts() {
      const start = (this.currentPage - 1) * this.productsPerPage;
      const end = start + this.productsPerPage;
      return this.filteredProducts.slice(start, end);
    }

  },

  beforeMount() {
    this.getProduct();
  },
};
</script>

<template>
  <div class="content-area">
    <div class="left-sidebar">
      <div class="filter-group">
        <div class="label">
          <span>Availability</span>
        </div>
        <div class="items">
          <label class="filter" v-for="status in availableProductStatus">
            <input type="checkbox" :value="status" v-model="selectedStatuses" />
            <span>{{ status }}</span>
          </label>
        </div>
      </div>

      <div class="filter-group">
        <div class="label">
          <span>Price</span>
        </div>
        <div class="items price-area">
          <label class="range-label from">
            <input type="number" id="range-to" v-model.number="priceFrom"/>
          </label>
          <label class="range-label to">
            <input type="number" id="range-from" v-model.number="priceTo" />
          </label>
        </div>
      </div>

      <div class="filter-group">
        <div class="label">
          <span>Category</span>
        </div>

        <div class="items">
          <label
            class="filter"
            v-for="category in availableCategory"
            :key="category"
          >
            <input
              type="checkbox"
              :value="category"
              v-model="selectedCategories"
            />
            <span>{{ category }} </span>
          </label>
        </div>
      </div>

      <div class="filter-group">
        <div class="label">
          <span>Brand</span>
        </div>
        <div class="items">
          <label class="filter" v-for="brand in availableBrands" :key="brand">
            <input type="checkbox" :value="brand" v-model="selectedBrands" />
            <span>{{ brand }}</span>
          </label>
        </div>
      </div>
    </div>
    <div class="right-area">
      <div class="top-bar ws-box">
        <div class="sorting-filtering">
          <div class="">
            <label class="page-heading m-hide">All Products</label>
          </div>
          <div class="show-sort">
            <div class="form-group">
              <label>Show:</label>
              <div class="custom-select">
                <select id="input-limit" v-model="productsPerPage">
                  <option v-for="count in pageOptions" :key="count" :value="count">{{ count }}</option>
                </select>
              </div>
            </div>
            <div class="form-group">
              <label>Sort By:</label>
              <div class="custom-select">
                <select id="input-sort" v-model="sortOption">
                  <option value="">Default</option>
                  <option value="lowToHigh">Price (Low &gt; High)</option>
                  <option value="highToLow">Price (High &gt; Low)</option>
                </select>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="main-content p-items-wrap">
        <div class="p-item" v-for="product in paginatedProducts">
          <div class="p-item-inner">
            <div class="marks">
              <span class="mark">Save: {{ product.discountPercentage }}%</span>
            </div>
            <div class="p-item-img">
              <a href="#">
                <img
                  :src="product.thumbnail"
                  :alt="product.title"
                  width="228"
                  height="228"
                />
              </a>
            </div>
            <div class="p-item-details">
              <h4 class="p-item-name">
                <a href="#">{{ product.title }}</a>
              </h4>
              <div class="short-description">
                <!-- <p>{{ product.description.substring(0,80) }}...</p> -->
                <ul>
                  <li><b>Brand:</b> {{ product.brand }}</li>
                  <li><b>Category:</b> {{ product.category }}</li>
                  <li><b>Stock:</b> {{ product.stock }}</li>
                  <li>
                    <b>Tags: </b>
                    <span v-for="tag in product.tags"> {{ tag }}, </span>
                  </li>
                </ul>
              </div>
              <div class="p-item-price">
                <span class="price-new">${{ product.price }}</span>
                <!-- <span class="price-old">62,000৳</span> -->
              </div>
              <div class="actions">
                <span class="st-btn btn-add-cart" type="button"> Buy Now</span>
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
.left-sidebar {
  width: 20%;
  margin-right: 20px;
}
.right-area {
  width: 80%;
}
.items {
  display: flex;
  flex-direction: column;
  padding: 10px 20px 10px;
  margin-right: 0;
  max-height: 300px;
  overflow-y: auto;
}
.filter-group {
  background-color: white;
  margin-bottom: 10px;
}
.label {
  padding: 0 0 0 20px;
  height: 50px;
  line-height: 50px;
  cursor: pointer;
  color: #111;
  font-size: 17px;
  border-bottom: 1px solid #eee;
}

.filter-group .items label.filter span {
  display: block;
  margin-left: 25px;
  line-height: 20px;
  color: #111;
  text-transform: capitalize;
}
.filter-group .items label.filter input {
  margin-right: 10px;
  height: 16px;
  width: 16px;
  position: relative;
  top: 2px;
  float: left;
}
.filter-group .items label.filter:hover {
  background: #f2f4f8;
}
.filter-group .items label.filter {
  display: inline-block;
  width: 100%;
  padding: 6px;
  margin: 0 -6px;
  font-size: 14px;
  border-radius: 3px;
  cursor: pointer;
}
.top-bar {
  padding: 10px 10px 10px 20px;
  margin-bottom: 10px;
}
.top-bar .page-heading {
  font-size: 16px;
  line-height: 30px;
  font-weight: bold;
}
.top-bar .show-sort {
  text-align: right;
}
.show-sort .form-group {
  display: inline-block;
  padding-left: 10px;
  margin-bottom: 0;
}
.show-sort .form-group label {
  padding-right: 5px;
  color: #666;
}
.show-sort .form-group select {
  background: #f1f3f5;
  padding: 6px 5px;
  font-size: 14px;
  border: none;
  position: relative;
  outline: none;
  height: 30px;
  max-width: 110px;
}
select option {
  font-size: 15px;
  line-height: 30px;
}
.ws-box {
  background: #fff;
  border-radius: 5px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
}
.sorting-filtering {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.p-items-wrap {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -5px;
  padding: 0;
  justify-content: flex-start;
}
.p-item {
  flex: 0 0 20%;
  max-width: 20%;
  padding: 0 5px 10px;
  margin-bottom: 0;
  display: flex;
  position: relative;
}
.p-item-inner {
  display: flex;
  flex-direction: column;
  position: relative;
  width: 100%;
  background: #fff;
  border-radius: 5px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
}
.marks {
  display: flex;
  position: absolute;
  top: 15px;
  left: 0;
  z-index: 10;
  flex-direction: column;
  align-items: flex-start;
}
.marks .mark {
  background: #6e2594;
  width: auto;
  color: #fff;
  font-size: 12px;
  padding: 3px 10px;
  line-height: 14px;
  margin-bottom: 2px;
  border-radius: 0 20px 20px 0;
  flex: 0 0 auto;
}
.p-item-img {
  text-align: center;
  border-bottom: 3px solid rgba(55, 73, 187, 0.03);
  flex: 0 0 220px;
  padding: 20px;
  margin: 0;
}
.p-item-img img {
  max-width: 100%;
}
.p-item-details {
  padding: 15px;
  flex: 1 1 auto;
  display: flex;
  flex-direction: column;
}
.p-item-name {
  margin: 0 0 15px;
  line-height: 20px;
  overflow: hidden;
  font-weight: 600;
  font-size: 14px;
  position: relative;
  height: auto;
}
.p-item .short-description {
  padding: 10px 0 0 14px;
  flex: 1 1 auto;
  border-bottom: 1px solid #eee;
  margin-bottom: 5px;
}
.short-description ul {
  padding-left: 0px;
}
.short-description li {
  font-size: 13px;
  color: #666;
  position: relative;
  line-height: 16px;
  padding-bottom: 10px;
  list-style: none;
}
.p-item .p-item-price {
  padding-top: 10px;
  flex: 0 0 22px;
  text-align: center;
}

.p-item-price {
  line-height: 22px;
  font-size: 17px;
  font-weight: 600;
  color: #ef4a23;
}
.p-item-price .price-old {
  font-size: 12px;
  font-weight: 600;
  text-decoration: line-through;
  color: #666;
  padding-left: 5px;
}
.p-item .actions {
  flex: 0 0 60px;
  display: inline-block;
  width: 100%;
  padding: 15px 0 10px;
}
.p-item .actions .st-btn {
  display: flex;
  flex-wrap: nowrap;
  line-height: 34px;
  background: #fff;
  background: rgba(55, 73, 187, 0.05);
  color: #3749bb;
  border-radius: 4px;
  font-size: 13px;
  font-weight: 600;
  padding: 0 14px;
  cursor: pointer;
  justify-content: center;
  align-content: center;
  text-decoration: none;
}
.p-item .actions .st-btn:hover {
  background: #3749bb;
  color: #fff;
}
.range-label input {
  float: left;
  width: 80px;
  height: 30px;
  border: 1px solid #c5cbd5;
  margin: 5px 0 0 -1px;
  outline: 0;
  padding: 6px 0;
  border-radius: 0;
  font-size: 14px;
  text-align: center;
}
.price-area {
  flex-direction: row;
  justify-content: space-around;
}
</style>
