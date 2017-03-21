<template>
  <form>
    {{message}}
    <slot>我是替补内容</slot>
    <table class="detial-wrap">
      <tr>
        <th>商品信息</th>
        <th>商品金额</th>
        <th>商品数量</th>
        <th>总金额</th>
        <th>编辑</th>
      </tr>
      <tr v-for="(item,index) in cartList.result">
        <td class="goods-detial-wrap">
          <div class="checkbox-wrap left"><span class="checkbox" v-bind:class="{'checked':item.checked}" v-on:click="checkItem(item)"></span></div>
          <div class="goods-detial right">
            <div class="good-img left"><img v-bind:src="item.imgPic" /></div>
            <div class="good-text left">
              <div class="name">{{item.name+' ------ '+index}}</div>
              <dl class="gifts">
                <dt>赠送：</dt>
                <dd>{{item.gifts}}</dd>
              </dl>
            </div>
          </div>
        </td>
        <td class="unitprice">{{item.price | formatMoney('元')}}</td>
        <td class="buy-num">
          <div class="choosenum-handler">
              <i class="icon-minus" v-on:click="mins(item)"></i>
              <input type="text" name="" value="0" class="countbox" v-model="item.count">
              <i class="icon-plus" v-on:click="maxs(item)"></i>
          </div>
          <div class="stock onsell"></div>
        </td>
        <td class="amount">{{item.price * item.count | formatMoney('元')}}</td>
        <td class="icon icon-delete" v-on:click="del(cartList,index)"></td>
      </tr>
    </table>
    <footer class="checkout-wrap">
      <div class="total-check-wrap left">
        <div class="checkbox-wrap left"><span class="checkbox" v-bind:class="{'checked':this.checkAll}" v-on:click="checkAllFlag()"></span></div>
        <div class="check-text"><span class="checked-all">全选</span><span class="unchecked-all">取消全选</span>
        </div>
      </div>
      <div class="checkout right">
        <div class="total-money"><span class="name">总金额 :</span><span class="amount">{{ getTotalCount() | round}}元</span></div>
        <input type="submit" value="结账" class="danger"/>
      </div>
    </footer>
  </form>
</template>

<script>
export default {
    name:'Hello',
    props:['message'],
    data(){
        return {
            cartList:[],
            checkAll:false
        }
    },
    mounted(){
        this.getCartList();
    },
    filters:{
        formatMoney:function(value,type){
            return '$'+value.toFixed(2)+type;
        },
        round:function(value){
            return value.toFixed(2);
        }
    },
    methods:{
        getCartList:function(){
            let _this = this;
            this.$http.get('/static/resource/cart.json').then(res=>{
                _this.cartList = res.body;
            })
        },
        mins:function(item){
            if(item.count>0){
                item.count--;
                this.$emit('change');
            }else{
                item.count=0;
            }
        },
        maxs:function(item){
            item.count++;
            this.$emit('change');
        },
        del:function(cartList,index){
            cartList.result.splice(index,1)
        },
        getTotalCount:function(){
            var sum=0;
            this.cartList.result.forEach(function(value,key){
                sum += value.count * value.price;
            })

            return sum;
        },
        checkItem:function(item){
            if(item.checked == undefined){
                this.$set(item,'checked',true);
            }else{
                item.checked = !item.checked;
            }
        },
        checkAllFlag:function(){
            this.checkAll = !this.checkAll;
            var item = this.cartList.result;
            item.forEach((value,key)=>{
                value.checked == undefined?this.$set(value,'checked',true):value.checked = !value.checked;
            });
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<!--<style scoped>-->
<!--h1, h2 {-->
  <!--font-weight: normal;-->
<!--}-->

<!--ul {-->
  <!--list-style-type: none;-->
  <!--padding: 0;-->
<!--}-->

<!--li {-->
  <!--display: inline-block;-->
  <!--margin: 0 10px;-->
<!--}-->

<!--a {-->
  <!--color: #42b983;-->
<!--}-->
<!--</style>-->
