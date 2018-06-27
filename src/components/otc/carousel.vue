<template>
  <div>
    <Carousel autoplay v-model="value2" loop :autoplay-speed="3000" id="carousel">
        <!-- <CarouselItem>
            <div class="demo-carousel"><img src="../../assets/img/banner.jpg" alt=""></div>
        </CarouselItem> -->
        <CarouselItem v-for="(item,index) in imgList" :key="index">
            <div class="demo-carousel">
               <div class='CarouselText'>
                    <!-- <h3>领衔全球的数字资产交易平台</h3>
                    <p>领衔全球的数字资产交易平台领衔全球的数字资产交易平台领衔全球的数字资产交易平台领衔全球的数字资产交易平台</p> -->
                    <h3>{{item.name}}</h3>
                    <p>{{item.remark}}</p>
                    <!-- <a href='item.linkUrl' class='btnBlue'>去交易</a> -->
                    <!-- <router-link to="/otc/trade"  class='btnBlue'>{{$t('common.goTrade')}}</router-link> -->
                </div>
                <img  :src=item.url>
            </div>
        </CarouselItem>
    </Carousel>
        
  </div>
</template>
<script>
export default {
    data () {
        return {
            value2: 0,
            imgList:[] 
        }
    },
    created:function(){
         this.getImg();
    },
    methods:{
        getImg(){
        
            this.$http.post(this.host+this.api.uc.banner,{sysAdvertiseLocation:1}).then(response => {
                var resp=response.body.data;
                this.imgList=resp;
            })
        }
   }
}
</script>
<style scoped>
#carousel img{
  width: 100%;
  /* height: 500px; */
}
</style>


