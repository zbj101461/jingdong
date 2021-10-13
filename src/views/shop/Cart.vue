<template>
 <div class="mask"
 v-if="showCart"
  @click="handleCartShowChange"
 />
  <div class="cart">
      <div class="product" v-if="showCart">
          <div class="product__header">
              <div class="product__header__all" 
              @click="()=>setCartItemChecked(shopId)"
              >
                  <span class="product__header__icon iconfont"
                  v-html="allChecked ?'&#xe618;':'&#xe61a;'"
                  >
                </span>
                  全选
                </div>
              <div class="product__header__clear">
              <span class="product__header_clear__btn" 
              @click="()=>cleanCartProducts(shopId)"
            >
                  清空购物车
                  </span>
              </div> 
          </div>
          <template
           
            v-for="(item) in productList"
            :key="item._id"
            >
            <div  class="product__item" v-if="item.count>0">
                <div class="product__item__checked iconfont"
                v-html="item.check ? ' &#xe618;':'&#xe61a;'"
                @click="()=>changeCartItemChecked(shopId,item._id)"
                />
                <img class="product__item__img" :src="item.imgUrl" />
                <div class="product__item__detail">
                    <h4 class="product__item__title">{{item.name}}</h4>
                    <p class="product__item__price">
                        <span class="product__item__yen">&yen;</span>{{item.price}}
                        <span class="product__item__origin">&yen;{{item.oldPrice}}</span>  
                    </p>
                </div>
                <div class="product__number">
                    <span class="product__number__minus"
                      @click="() => { changeCartItemInfo(shopId, item._id, item,-1) }"
                    >-</span>
                    {{item.count || 0}}
                    <span class="product__number__plus"
                         @click="() => { changeCartItemInfo(shopId, item._id, item,1) }"
                    >+</span>
                </div>
            </div>
            </template>
      </div>
    <div class="check">
        <div class="check__icon">
            <img src="http://www.dell-lee.com/imgs/vue3/basket.png" 
            class="check__icon__img"
            @click="handleCartShowChange"
            />
            <div class="check__icon__tag">{{total}}</div>
        </div>
        <div class="check__info">
            总计：<span class="check__info__price">&yen;{{price}}</span>
        </div>
        <div class="check__btn">
            <router-link :to="{name:'Home'}">
            去结算
            </router-link>
        </div>
    </div>
  </div>
</template>

<script>
import {computed,ref} from 'vue'
import {useStore} from 'vuex'
import {useRoute} from 'vue-router'
import {useCommonCartEffect} from './commonCartEffect'

// 获取购物车相关逻辑
const useCartEffect=(shopId)=>{
     const {changeCartItemInfo}=useCommonCartEffect()
      const store=useStore()
      const cartList=store.state.cartList
      const total=computed(()=>{
            const productList=cartList[shopId]
           let count=0
            if(productList){
                 for(let i in productList){
                     const product=productList[i]
                     count+=product.count
            }
         }
           
            return count
        })

         const price=computed(()=>{
            const productList=cartList[shopId]
           let count=0
            if(productList){
                 for(let i in productList){
                     const product=productList[i]
                    if(product.check){
                    count =product.count*product.price 
                     }
                
            }
         }
           
            return count.toFixed(2)
        })
       
     
      const allChecked=computed(()=>{
            const productList=cartList[shopId]
           let result=true
            if(productList){
                 for(let i in productList){
                     const product=productList[i]
                    if(product.count>0 && !product.check){
                        result=false
                     }
                }
         }
           
            return result
        })

        const productList=computed(()=>{
             const productList=cartList[shopId] || []
             return productList
        })
        const changeCartItemChecked=(shopId,productId)=>{
        store.commit('changeCartItemChecked', {
            shopId,productId,
        })

    }
      const cleanCartProducts=(shopId)=>{
           store.commit('cleanCartProducts', {shopId})
      }

      const setCartItemChecked=(shopId)=>{
          store.commit('setCartItemChecked',{shopId})
      }
        return{
            total,price,productList,
            changeCartItemInfo,changeCartItemChecked,
            cleanCartProducts, allChecked,setCartItemChecked
        }
}
// 展示隐藏购物车逻辑
    const toggleCartEffect=()=>{
      const showCart=ref(false)
      const handleCartShowChange=()=>{
          showCart.value=!showCart.value
      }
      return{showCart,handleCartShowChange}

    }
export default {
    name:'Cart',
    setup(){
      const route=useRoute();
      const shopId=route.params.id;
      const {showCart,handleCartShowChange}=toggleCartEffect()
     
      const {
          total,price,productList,changeCartItemInfo,
          changeCartItemChecked,cleanCartProducts,allChecked,setCartItemChecked
          }=useCartEffect(shopId)
      return{
          total,price,productList,changeCartItemInfo,
          shopId,changeCartItemChecked,cleanCartProducts,
          allChecked,setCartItemChecked,showCart,handleCartShowChange
          }

        
    }
}
</script>

<style lang="scss" scoped>
@import '../../style/viriables.scss';
@import '../../style/mixins.scss';

.mask{
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    background:  rgba(0,0,0,0.50);
    z-index: 1;
}

.cart{
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 2;
    background: $bg-Color;
}
.product{
    flex: 1;
    background: $bg-Color;
    overflow-y: scroll;
    &__header{
        display: flex;
        line-height: .52rem;
       border-bottom:.01rem solid $content-bgColor;
        font-size: .14rem;
        color: $content-fontcolor;
       &__clear{
           flex: 1;
           text-align: right;
           margin-right: .18rem;
           &__btn{
               display: inline-block;
           }
        }
    &__all{
        width: .64rem;
        margin-left: .18rem;
    }
    &__icon{
        color: $btn-bgColor;
        font-size: .2rem;
        display: inline-block;
        vertical-align: top;
        margin-right: .1rem;
    }
    }
    &__item{
        display: flex;
        padding: .12rem 0;
        margin: 0 .16rem;
        border-bottom: .01rem solid $content-bgColor;
        position: relative;
        &__checked{
            color:$btn-bgColor;
            font-size: .2rem;
            line-height: .5rem;
            margin-right: .2rem;
        }
        &__detail{
            overflow: hidden;
        }
        &__img{
            width: .46rem;
            height: .46rem;
            margin-right:.16rem;
        }
        &__title{
            font-size: .14rem   ;
            color: $content-fontcolor;
            line-height: .2rem;
            margin: 0;
            @include ellipsis;
        }
        &__price{
            margin: .06rem 0 0 0;
            font-size: .14rem;
            color: $hightlight-fontColor;
            line-height: .2rem;
        }
        &__yen{
            font-size: .12rem;
        }
        &__origin{
            line-height: .2rem;
            font-size: .12rem;
            color: $light-fontColor;
            text-decoration: line-through;
            margin-left: .06rem ;
        }
        .product__number{
           position: absolute;
           right: 0;
           bottom: .26rem;
           &__minus,&__plus
           {
               display: inline-block;
               width: .2rem;
               height: .2rem;
               border-radius:50% ;
              
               font-size: .2rem;
               text-align: center;
               line-height: .16rem;
           }
            &__minus{
                 border: .01rem solid $medium-fontColor;
                 color:$medium-fontColor ;
                 margin-right: .05rem;
            }
           &__plus{
               background: $btn-bgColor ;
               color: $bg-Color;
                margin-right: .05rem;
           }
        }
    }

}
.check{
    height: .49rem;
    display: flex;
    border-top: .01rem solid $content-bgColor;
    line-height: .5rem;
    &__icon{
        position: relative;
        width: .84rem;
        &__img{
            width: .28rem;
            height: .26rem;
            margin: .12rem auto;
            display: block;
         }
         &__tag{
             position: absolute;
             width: .2rem;
             height: .2rem;
             background-color: $hightlight-fontColor;
             border-radius: 50%;
             line-height: .2rem;
             text-align: center ;
             right: .2rem;
             top: .04rem;
             color: $bg-Color;
             transform: scale(.5);

         }
    }
    &__info{
        flex: 1;
        color: $content-fontcolor;
        font-size: .12rem;
        &__price{
            color: $hightlight-fontColor;
            font-size: .18rem;
        }
    }
    &__btn{
        width: .98rem;
        background-color: #4FB0F9;
        font-size: .14rem;
        text-align: center;
        a{
          color: $bg-Color;
          text-decoration: none;
        }
    }
}
</style>
