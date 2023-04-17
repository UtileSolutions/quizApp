<template>
  <div class="home">
    <img class="vue-logo" alt="Vue logo" src="../assets/logo.png">
    <QuizContent :showScore="showScore" />
    <div>
      <input class="submit-btn" type="submit" value="Check score" @click="showScore = true" >
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import QuizContent from '@/components/QuizContent.vue'; // @ is an alias to /src

@Options({
  components: {
    QuizContent,
  },
})
export default class HomeView extends Vue {
  data() {
    return {
      showScore: false
    }
  }
}
</script>

<style>
.vue-logo {
  height: 100px;
}

.home {
  margin: 40px;
}

.submit-btn {
  padding: 24px;
  margin: 40px;
  background-color: rgb(58, 118, 231);
  color: white;
  font-size: 20px;
  border: 0px solid;
  cursor: pointer;
}

.submit-btn:hover {
  background-color: #333;
  box-shadow: 0px 2px 10px rgba(0,0,0,0.2);
}
</style>
