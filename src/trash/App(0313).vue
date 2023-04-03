<template>
  <!-- {{ JSON.parse(JSON.stringify(Carriers)) }} -->
  {{isBtn}}, {{ t_code }},{{ Trackings.invoiceNo }}
  {{ Trackings }}
  <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto my-40 flex bg-white rounded items-center pt-2 flex-wrap">
    <div class="border-b basis-full py-2 px-5 flex justify-center items-center text-sm">
      <span class="basis-[30%] text-center mr-5">국내 / 국외 선택</span>
      <button 
      @click="isBtn = 1; t_code='04'" :class="isBtn === 1 ? 'bg-slate-400 text-white' : 'text-black'" 
      class="text-sm border p-1 px-5 rounded hover:text-white hover:bg-slate-400 mr-4">국내</button>
      <button 
      @click="isBtn = 2; t_code='12'" :class="isBtn === 2 ? 'bg-slate-400 text-white' : 'text-black'" 
      class="text-sm border p-1 px-5 rounded hover:text-white hover:bg-slate-400">해외</button>
    </div>
       <!--v-model을 주는 이유 바인딩을 하기위해(추가로 안해주면 값이 역순으로 나옴(value 값 변화로 인해서 역순으로 나오는데 그게 v-model 을 통해서 바인딩되어서 역순을 막아주는거 같음)) -->
      <select v-model="t_code">
        <!-- 첫 시작 할때는 Carriers 에서 반복문 -> 국내,국외 클릭시 Company(필터로 국내/국외이면 국내/국외회사만 출력) 에서 반복문-->
        <option v-for="e in Company" :key="e" :value="e.Code">{{ e.Name }}</option>
      </select>
      <div class="basis-full py-4 text-center border-b">
        <input type="text" placeholder="운송장번호입력" 
        class="border w-[80%] px-5 py-3 bg-slate-400 outline-indigo-300"
        v-model="t_invoice" @input="bindNumber">
      </div>
      <div class="w-full text-center">
        <button class="w-full bg-indigo-400 text-white py-3"
        @click="PostList()">조회하기</button>
      </div>
  </div>

  
  
  
   <form action="http://info.sweettracker.co.kr/tracking/5" method="post">
      <div class="form-group">
        <label for="t_key">API key</label>
        <input type="text" class="form-control" id="t_key" name="t_key" placeholder="제공받은 APIKEY">
      </div>
      <div class="form-group">
        <label for="t_code">택배사 코드</label>
        <input type="text" class="form-control" name="t_code" id="t_code" placeholder="택배사 코드">
      </div>
      <div class="form-group">
        <label for="t_invoice">운송장 번호</label>
        <input type="text" class="form-control" name="t_invoice" id="t_invoice" placeholder="운송장 번호">
      </div>
      <button type="submit" class="btn btn-default">조회하기</button>
  </form>
  
</template>

<script>
// templates
import CarriterList from './assets/company.json';
import Tracking from './assets/tracking.json';
// 로컬로 돌리기 위해 주석 처리
// import axios from 'axios';

export default {
  name: 'App',
  components: {
    
  },
  data() {
    return {
      t_key: "ki6NR3qhneOeUH5bQhIT7w",
      t_code: "04",
      t_invoice: "363804378896",
      // 로컬로 저장한 json 파일을 CarriterList 을 저장
      Carriers: CarriterList,
      // 국내 국외 선택
      isBtn: 1,
      // 로컬로 저장한 tracking 상태
      Trackings: Tracking,
    }
  },
  created() {
    // 로컬로 돌리기 위해 주석 처리
    // axios.get("https://info.sweettracker.co.kr/api/v1/companylist?t_key=ki6NR3qhneOeUH5bQhIT7w")
    // .then((res)=>{
    //   this.Carriers = res.data.Company
    //   console.log(this.Carriers)
    // })
    // .catch((error)=>{
    //   console.log(error)
    // })
  },
  computed: {
    // 국내 국외 택배사 변경을 위해 for 문에서 돌기 위해
    Company() {
      return this.Carriers.filter((data)=>{
        if(this.isBtn === 1){
          // flase true 에 ''주의
          return data.International == 'false'
        }else{
          return data.International == 'true'
        }
      })
    }
  },
  watch:{
 
  },
  // v-model 를 사용할거면 watch를 사용해야함 @input 이라서 method 사용
  methods:{
    bindNumber() {
      return this.t_invoice = this.t_invoice.replace(/[^0-9]/g, '')
    },
    // 조회하기 클릭시 상태 요청하기(로컬로보기 위해 잠시 주석처리)
    // PostList(){
    //   axios.get("https://info.sweettracker.co.kr/api/v1/trackingInfo",{
    //     params:{
    //       t_code: this.t_code,
    //       t_invoice: this.t_invoice,
    //       t_key: this.t_key
    //     }
    //   }).then((res)=>{
    //     console.log(res)
    //   }).catch((error)=>{
    //     console.log(error)
    //   })
    // }
  }
}

</script>

<style>

</style>
