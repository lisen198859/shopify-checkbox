<template>
  <div id="cus">
    <b style="font-size: 1rem;line-height: 42px">Add Accessories（Tick to add to cart, Extra 10% OFF)</b>
    <el-checkbox @change="handleChange(product)" v-for="product in products" v-model="product.checked" :key="product.index">
      <img :src="product.imgUrl"/>
      <span><span style="font-size: 16px;font-weight: bold">{{product.title}}</span><br>
        <b>{{product.price}}</b><del>{{product.oldPrice}}</del><u @click.p.prevent="handleView(product.imgUrl)">View Larger</u>
      </span>
    </el-checkbox>
  </div>
</template>

<script>
import * as cart from '@shopify/theme-cart'
import { Checkbox, MessageBox } from 'element-ui'
import axios from 'axios'

export default {
  props: {
    urlLink: {
      type: String,
      default: 'babeside'
    }
  },
  components: {
    ElCheckbox: Checkbox
  },
  data: function () {
    return {
      products: []
    }
  },
  created () {
    this.init()
  },
  methods: {
    async init () {
      console.log(this.urlLink)
      const url = 'https://lt-shopify-app.oss-us-west-1.aliyuncs.com/' + this.urlLink + '.json'
      const res = await axios.get(url)
      this.products = res.data
    },
    handleView (value) {
      MessageBox.alert('<img width="290px" src="' + value + '"/>', {
        customClass: 'cus-mbox',
        dangerouslyUseHTMLString: true,
        showConfirmButton: false,
        closeOnClickModal: true
      })
    },
    handleChange (product) {
      const that = this
      console.log(product)
      if (product.checked) {
        cart.addItem(product.variant_id, { }).then(item => {
          console.log(item)
          that.products[product.index].key = item.key
          console.log(that.products[product.index].key)
          console.log(
            'An item with a quantity of 1 was added to your cart:',
            item
          )
        })
      } else {
        cart.removeItem(that.products[product.index].key).then(state => {
          console.log(`There is now ${state.items.length} item(s) in your cart`)
        })
      }
    }
  }
}
</script>
<style>
.cus-mbox{
  width: 320px;
}
</style>
<style lang="stylus" scoped>
#cus .el-checkbox{
  padding-top 15px
  padding-bottom 15px
  display flex
  align-items center
  width 100%
  border-top 1px solid #e4e4e4
}
#cus>>>.el-checkbox__label{
  display flex
  align-items center
  img{
    width 90px
    min-width: 90px;
    max-width: 90px;
    flex-grow 0
  }
  span{
    font-size 12px
    line-height 24px
    white-space normal
    margin-left 10px
    flex-grow 1
    b{
      color red
      font-size 14px
    }
  }
  u{
    margin-left 10px
  }
}
</style>
