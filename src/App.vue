<template>
  <div id="cus">
    <b style="font-size: 1rem;line-height: 42px">Add Accessories（Tick to add to cart, Extra 10% OFF)</b>
    <div class="list-box">
      <el-checkbox @change="handleChange(product)" v-for="product in products" v-model="product.checked" :key="product.index">
        <img :src="product.imgUrl"/>
        <span class="content"><span class="title">{{product.title}}</span><br>
<!--        <b>{{product.price}}</b><del>{{product.oldPrice}}</del><u @click.p.prevent="handleView(product.imgUrl)">View Larger</u>-->
      </span>
      </el-checkbox>
    </div>
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
/*.cus-mbox{*/
/*  width: 320px;*/
/*}*/
</style>
<style lang="stylus" scoped>
.el-checkbox__input.is-checked .el-checkbox__inner, .el-checkbox__input.is-indeterminate .el-checkbox__inner {
  background-color: #e81f76;
  border-color: #e81f76;
}
#cus .list-box{
  display flex
  align-items center
  flex-wrap wrap
  >>>.el-checkbox__inner{
         border-radius 50px
       }
  >>>.el-checkbox{
    width 33.33%
    display flex
    flex-direction column-reverse
    margin-right 0
  }
  >>>.el-checkbox.is-checked{
    img{
      box-sizing border-box
      border 1px solid #e81f76
      border-radius 4px
    }
    span.title{
      color #e81f76
    }
  }
  >>>.el-checkbox__label{
    display flex
    flex-direction column
    padding-left 0
    img{
      margin-bottom 10px
    }
    span.content {
      padding-left 10px
      padding-right 10px
      margin-left 0
      line-height 10px
    }
    span.title{
      font-size 12px
      line-height 16px
      overflow : hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      margin-left 0
    }
  }
}
#cus .el-checkbox{
  padding-top 15px
  padding-bottom 15px
  display flex
  align-items center
  width 100%
  //border-top 1px solid #e4e4e4
}
#cus>>>.el-checkbox__label{
  display flex
  align-items center
  img{
    width 110px
    min-width: 110px;
    max-width: 110px;
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
