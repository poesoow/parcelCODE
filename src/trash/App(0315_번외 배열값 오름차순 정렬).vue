<template>

  <div>
    <p>{{ arr }}</p>
    <p>{{ [...this.arr] }}</p>

    <div class="flex justify-center">
      <select @click="sortABC" name="" id="">
        <option v-for="e in arr" :key="e" value="e">{{ e.name }}</option>
      </select>
    </div>
  </div>
  

  <h3 class="text-center text-5xl  mt-10">택배조회 조회 실습하다가 배열 값 순서 오름차순 정렬</h3>

  
</template>

<script>
export default {
  name: 'App',
  components: {
    
  },
  data() {
    return {
      arr :[
        { name: 'banana', price: 3000 },
        { name: 'apple', price: 1000 },
        { name: 'orange', price: 500 },
        { name: '사과', price: 500 },
        { name: '바나나', price: 500 },
        { name: '가', price: 500 },
        { name: '나', price: 500 },
      ],

    }
  },

  mounted() {


    
      this.arr.sort(function (a, b) {
        return a.name.localeCompare(b.name)
      });

  },
}

</script>

<style>

</style>
