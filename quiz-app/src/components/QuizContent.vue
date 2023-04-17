<template>
    <div v-show="isLoading">
        <img class="loading-content" alt="loading content" src="../assets/Loading_icon.gif">
    </div>
    <div v-show="!showScore" class="quiz-content">
        <div  v-for="(element,questionIndex) in QuizContent.results" class="quiz-box">
            <p class="quiz-question">{{decodeHtml(element.question)}}</p>
            <div  class="box-grid">
                <label v-for="answer in element.incorrect_answers" :class="'label-'+questionIndex">
                    {{decodeHtml(answer)}}
                    <input type="radio" value="answer" hidden="hidden" @click="input(questionIndex, answer)"/>
                </label>
                <label :class="'label-'+questionIndex">
                    {{decodeHtml(element.correct_answer)}}
                    <input type="radio" value="answer.correct_answer" hidden="hidden" @click="input(questionIndex, element.correct_answer)"/>
                </label>
            </div>
        </div>
    </div>
    <div v-show="showScore">
        Your score was: {{ userScore }}
    </div>

</template>

<script>
  export default {
    name: 'QuizContent',
    data() {
      return {
        QuizContent: [],
        UserAnswers: [],
        userScore: 0,
        counter: 0,
        isLoading: false
      }
    },
    props: {
        showScore: false,
    },
    methods: {
      async getData() {
        this.isLoading = true;
        const apiResponse = await fetch("https://opentdb.com/api.php?amount=10&type=multiple&difficulty=easy");
        const res = await apiResponse.json();
        this.QuizContent = res;
        this.isLoading = false;
      },
      input(questionIndex, answer) {
        if (this.questionAnswered(questionIndex) == true) {
            this.updateInput(questionIndex, answer);
        } else {
            this.saveInputNew(questionIndex, answer);
        }
      },
      questionAnswered(index) {
        if (this.UserAnswers.length == 0) {
            return false;
        } else if (this.UserAnswers.length > 0) {
            var foundAnswer = false;
            this.UserAnswers.forEach(answer => {
                if (answer.i == index) {
                    foundAnswer = true;
                }
            });
            return foundAnswer;
        }
      },
      updateInput(index, answer) {
        this.UserAnswers.forEach(inputElements => {
            if (inputElements.i == index) {
                inputElements.a = answer,
                inputElements.correct = this.checkAnswer(answer, this.QuizContent.results[index].correct_answer)
            }
        })
        this.getUserScore();
      },
      saveInputNew(index, answer) {
        this.selectedAnswer(index);
        this.UserAnswers.push({
            i: index,
            q: this.QuizContent.results[index].question,
            a: answer,
            correct: this.checkAnswer(answer, this.QuizContent.results[index].correct_answer)
        })
        this.getUserScore();
      },
      getQuestionInfo(index) {
      },
      checkAnswer(answer, correctAnswer) {
        return answer == correctAnswer ? true : false;
      },
      selectedAnswer(index) {
        /* eslint-disable */
            var className = "label-" + index;
            var options = document.getElementsByClassName(className);

            for (let element of options) {
                element.addEventListener("click", function() {
                    for (let label of options) {
                        label.classList.remove("selected");
                    }
                    element.classList.add("selected");
                });
            };
        },
      getUserScore() {
        var score = 0;
        this.UserAnswers.forEach(element => {
            if (element.correct == true) {
                score++;
            }
            
        });
        this.userScore = score;
      },
      decodeHtml(html) {
        var txt = document.createElement("textarea");
        txt.innerHTML = html;
        return txt.value;
      },
    },
    mounted() {
        this.getData();
    }
  }
</script>

<style scoped>
    .quiz-box {
        background-color: rgb(226, 101, 52);
        box-shadow: 0px 2px 10px rgba(0,0,0,0.2);
        border-radius: 5px;
        padding: 24px;
        margin: 40px;
        color: white;
        max-width: 600px;
        margin: 40px auto;
    }

    .quiz-question {
        font-size: 24px;
    }

    .box-grid {
        display: grid;
    }

    .box-grid label {
        padding: 16px;
        margin: 4px;
    }

    .box-grid label:hover {
        background-color: white;
        color: #333;
        border-radius: 5px;
        cursor: pointer;
    }

    .selected {
        background-color: rgb(100, 100, 249);
        color: white;
        font-weight: bold;
    }
</style>
