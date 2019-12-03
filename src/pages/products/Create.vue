<template>
<div class="q-my-xl">
    <q-card>
        <q-card-title>Create new product</q-card-title>
        <q-card-main>
            <q-field :count="250">
                <q-input float-label="Name" v-model="productName" max-length="250" />
            </q-field>
            <q-field :count="50">
                <q-input float-label="Price" v-model="productPrice" max-length="50" />
            </q-field>
            <q-field :count="50">
                <q-input float-label="Brand" v-model="productBrand" max-length="50" />
            </q-field>
            <q-field :count="50">
                <q-input float-label="In Stock" v-model="productStock" max-length="50" />
            </q-field>
            <q-select
                float-label="Category"
                v-model="select"
                :options="selectOptions"
            />
            <q-field :count="5000">
                <q-input
                    type="textarea"
                    float-label="Description"
                    v-model="productDescription"
                    :max-height="100"
                    rows="7"
                />
            </q-field>
            <q-field helper="Supported format: JPG, max. file size: 300KiB" class="q-mt-lg">
                <q-uploader url="http://127.0.0.1:8000/upload" float-label="Images" multiple extensions=".jpg" :additional-fields="[{name: 'name', value: productName}, {name: 'brand', value: productBrand}]" auto-expand/>
            </q-field>
        </q-card-main>
        <q-card-actions class="q-mt-md">
            <div class="row justify-end full-width docs-btn">
                <q-btn label="Cancel" flat to="/products/index"/>
                <q-btn label="Create" color="primary" @click="createProduct"/>
            </div>
        </q-card-actions>
    </q-card>
</div>
</template>

<style lang="stylus">
.docs-btn .q-btn {
  padding: 15px 20px;
}
</style>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      productName: '',
      productDescription: '',
      productPrice: '',
      productBrand: '',
      productStock: '',
      selectOptions: [],
      select: ''
    }
  },
  methods: {
    createProduct () {
      axios
        .post(`http://127.0.0.1:8000/products`, this.productData)
        .then(response => {
          this.$q.notify({ type: 'positive', timeout: 2000, message: 'The product has been created.' })
          this.$router.push({ path: '/products/' + response.data.id + '/edit' })
        })
        .catch(error => {
          this.$q.notify({ type: 'negative', timeout: 2000, message: 'An error has been occured.' })
          console.log(error)
        })
    }
  },
  computed: {
    productData: function () {
      return { name: this.productName, description: this.productDescription, price: this.productPrice, category_id: this.select, in_stock: this.productStock, brand: this.productBrand, category: this.select }
    }
  },
  mounted () {
    axios
      .get(`http://127.0.0.1:8000/products/create`)
      .then(response => {
        this.selectOptions = response.data
        console.log(response.data)
      })
      .catch(error => {
        this.$q.notify({ type: 'negative', timeout: 2000, message: 'Loading categories: an error has occured.' })
        console.log(error)
      })
  }

}
</script>
