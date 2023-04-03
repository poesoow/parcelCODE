<template>
  <h3 class="text-center text-5xl  mt-10">택배조회서비스</h3>
  <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto mt-10 mb-20 flex bg-white rounded items-center flex-wrap">
    <div class="border-b basis-full py-2 px-5 flex justify-center items-center text-sm">
      <span class="basis-[31%] text-center mr-5">국내 / 국외 선택</span>
      <div class="flex justify-center w-2/5 md:w-2/5 xl:w-6/12">
        <button 
        @click="isBtn = 1; t_code='04'" :class="isBtn === 1 ? 'bg-slate-400 text-white' : 'text-black'" 
        class="border-[purple] text-sm border p-1 px-5 rounded hover:text-white hover:bg-slate-400 mr-4">국내</button>
        <button 
        @click="isBtn = 2; t_code='12'" :class="isBtn === 2 ? 'bg-slate-400 text-white' : 'text-black'" 
        class="border-[purple] text-sm border p-1 px-5 rounded hover:text-white hover:bg-slate-400">해외</button>
      </div>
    </div>
    <div class="border-b basis-full py-2 px-5 flex justify-center items-center">
      <div class="basis-[31%] text-center mr-5 text-sm">택배사 선택</div>
      <!--v-model을 주는 이유 바인딩을 하기위해(바인딩이 되는것은 화면상단에 {{ t_code }} 출력시 잘 보임 )(추가로 안해주면 값이 역순으로 나옴(value 값 변화로 인해서 역순으로 나오는데 그게 v-model 을 통해서 바인딩되어서 역순을 막아주는거 같음)) -->
      
      <select v-model="t_code" class="border-[purple] text-center border w-2/5 md:w-2/5 xl:w-6/12">
        <!-- 첫 시작 할때는 Carriers 에서 반복문 -> 국내/국외 클릭시 Company(필터로 국내/국외회사만 출력) 에서 반복문-->
        <option v-for="e in Company" :key="e" :value="e.Code">{{ e.Name }}</option>
      </select>
    </div>
    <div class="border-b basis-full py-2 px-5 flex justify-center items-center">
      <div class="basis-[31%] text-center mr-5 text-sm">운송장번호</div>
      <div class="w-2/5 md:w-2/5 xl:w-6/12 text-center border-b">
        <!-- // id 추가 -->
        <input type="text" placeholder="운송장번호입력" 
        class="border w-[100%] px-5 py-1 bg-slate-300 outline-indigo-300"
        v-model="t_invoice" @input="bindNumber" id="t_invoice">
      </div>
    </div>
    <div class="w-full text-center">
      <button class="w-full bg-indigo-400 text-white py-3"
      @click="[trackingStateChange(), PostList()]">조회하기</button>
    </div>
  </div>

  <p class="text-center text-2xl text-red-500 font-bold">{{ errorMsg }}</p>

  <!-- error -->
  <div v-if="trackingState === true && errorMsg === ''" class="w-full">
    <div class="flex justify-center py-10 px-5 flex-wrap items-center">
      <span class="text-right text-xl basis-[45%] font-bold mr-5 mb-5">운송장 번호</span>
      <h3 class="text-left text-2xl w-[45%] font-bold mb-5">{{ Trackings.invoiceNo }}</h3>
      <span class="text-right  text-xl basis-[45%] font-bold mr-5">택배사</span>
      <!-- [0]을 쓰는 이유 [] 배열표시를 지우기 위해 TrackingCode computed에서 새로운 배열로 만들고 map 문을 통해 원하는 값하나만을 넣은 배열로 존재 -->
      <h3 class="text-left  text-xl w-[45%] font-bold">{{ TrackingCode[0] }}</h3> 
    </div>
    <div class="my-5 flex justify-around">
      <div class="relative" v-for="(level) in 5" :key="level">
        <img :src="Trackings.level - 1 === level ? require(`@/assets/images/ic_sky_delivery_step${level}_on.png`) : require(`@/assets/images/ic_sky_delivery_step${level}_off.png`)">
        <p>{{ PostListName[level - 1] }}</p>
      </div>
    </div>
    <div class="py-5">
      <div class="font-bold text-lg pl-5 flex items-center">
        <div class="basis-[30%] text-center">
          <div>현재위치</div>
          <div>배송상태</div>
        </div>
        <div class="basis-[30%] text-center pl-[5%]">시간</div>
        <div class="basis-[30%] text-center pl-[15%]">연락처</div>
      </div>
      <div class="[&>:first-child]:text-blue-400">
        <div 
        v-for="(e, index) in Trackings.trackingDetails.slice().reverse()" :key="index" 
        class="py-5 odd:bg-slate-300 even:bg-slate-100 flex justify-around items-center">
          <div class="font-bold basis-[30%] text-center">
            <p>{{ e.where }}</p> 
            <p>{{ e.kind }}</p>
          </div>
          <div class="basis-[30%] text-center">{{ e.timeString }}</div>
          <div class="basis-[30%] text-center">{{ e.telno }}</div>
        </div>
      </div>
    </div>
  </div>

  
</template>

<script>
// 로컬로 작업한 Tracking 주석 처리
// import carrierList from './assets/company.json';
// import Tracking from './assets/tracking.json';
// 로컬로 돌리기 위해 주석 처리
import axios from 'axios';

export default {
  name: 'App',
  components: {
    
  },
  data() {
    return {
      t_key: "ki6NR3qhneOeUH5bQhIT7w",
      // 로컬 확인용
      t_code: "04",
      // 로컬 확인용
      // t_invoice: "363804378896",
      t_invoice: "",
      // 로컬로 저장한 json 파일을 carrierList 을 저장
      // Carriers: carrierList,
      // 데이터 가져올 것 배열로 변수 설정
      Carriers: [],
      // 국내 국외 선택
      isBtn: 1,
      // 로컬로 저장한 tracking 상태
      // Trackings: Tracking,
      // 데이터 가져올 것 배열로 변수 설정
      Trackings: [],
      // 배송상태 출력을 위한 값(API에 없기 때문에 직접 네이밍해야함)
      PostListName: ["상품인수", "상품이동중", "배송지도착", "배송출발", "배송완료"],
      // 배달상태 영역 기본 안나오게 하기 조회시 나오도록 하기 위한 변수 설정
      trackingState: false,
      errorMsg: '',
    }
  },
  created() {
    // 로컬로 돌리기 위해 주석 처리
    axios.get("https://info.sweettracker.co.kr/api/v1/companylist?t_key=ki6NR3qhneOeUH5bQhIT7w")
    .then((res)=>{
      this.Carriers = res.data.Company
      console.log("create", this.Carriers)
    })
    .catch((error)=>{
      console.log(error)
    })
  },
  mounted() {
    console.log("mount",  this.Trackings)
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
    },
    // 필터를 걸고 원하는 값만 나오게 하기 위해서 
    TrackingCode() {
      return this.Carriers.filter((e)=>{
        return e.Code === this.t_code
      }).map(e => e.Name);
      // 이하는 map 역할 확인 가능
      // }).map(e => e.Name);
      // })
    },
  },
  watch:{
 
  },
  // v-model 를 사용할거면 watch를 사용해야함 @input 이라서 method 사용
  methods:{
    bindNumber() {
      return this.t_invoice = this.t_invoice.replace(/[^0-9]/g, '')
    },
    // 조회하기 클릭시 상태 요청하기(로컬로보기 위해 잠시 주석처리)
    PostList(){
      // error(운송장번호 없는경우) 시 axios 실행 안되게 하기 위한 if 문 + html안에 id t_invoice 값 추가하여 focus() 커서를 이동시킴
      if(this.t_invoice === ''){
        this.errorMsg = "운송장 번호 입력해주세요"
        document.querySelector("#t_invoice").focus()
        return
      }
      axios.get("https://info.sweettracker.co.kr/api/v1/trackingInfo",{
        params:{
          t_code: this.t_code,
          t_invoice: this.t_invoice,
          t_key: this.t_key
        }
      }).then((res)=>{
        console.log("Postlist", res)
        if(res.data.code === '104'){
          this.errorMsg = res.data.msg
        }else{
          this.Trackings = res.data
          // 초기화 시키려고 함
          this.errorMsg = ''
          // 없어도 아래 나오네??? 
          // this.trackingState = true
        }
      }).catch((error)=>{
        console.log(error)
      })
    },
    trackingStateChange() {
      return this.trackingState = true;
    }
  }
}

</script>

<style>

</style>
