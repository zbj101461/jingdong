<template>
    <div class="docker">
    <div v-for="(item,index) in dockerList"
    :class="{'docker__item':true,'docker__item--active':index===0}"
    :key="item.icon"
    >
      <div class="iconfont" v-html="item.icon"/>
      <div class="docker__title">{{item.text}}</div>
      </div>
   </div>
</template>

<script>

export default {
    name:'Docker',
    setup(){
      const dockerList=[
        {icon:'&#xe619;',text:'首页'},
        {icon:'&#xe638;',text:'购物车'},
        {icon:'&#xea2a;',text:'订单'},
        {icon:'&#xe660;',text:'我的'}
    ];
    return { dockerList }
    }
}
</script>


<style lang="scss" scoped>
@import '../../style/viriables.scss';
.docker{
  display: flex;
  position: absolute;
  padding: 0 .18rem;
  left: 0;
  bottom: 0;
  width: 100%;
  height: .49rem;
  border-top: .01rem solid $content-bgColor;
  box-sizing: border-box;
  color:$content-fontcolor;
  &__item{
     flex: 1;
  text-align: center;
  .iconfont{
    font-size: .18rem;
    margin: .07rem 0 .02rem 0;
  }
  &--active{
      color: #1FA4FC ;
  }
  }
  &__title{
     font-size: .2rem;
  transform: scale(.5,.5);
  transform-origin: center top;
  }
}


</style>