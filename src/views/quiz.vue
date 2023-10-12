<template>
  <div class="quiz">
    <div class="quiz-wrapper">
      <div class="container">

        <!-- Progress bar -->
        <div
          class="progress"
          role="progressbar"
          aria-label="Quiz progress"
          aria-valuenow="actions.quizIndex + 1"
          aria-valuemax="9"
        >
          <div
            class="progress-bar progress-bar-striped progress-bar-animated bg-warning"
            :style="{width: ((actions.quizIndex + 1)*100)/9 + '%'}"
          >
          </div>
        </div>

        <div>
          <span>Question: {{ actions.quizIndex + 1 }}</span>/<span>9</span>
        </div>

        <!-- quiz question/answers -->
        <div class="title">{{ actions.quizIndex+1 +". "+ ourQuizs[actions.quizIndex].question }}</div>
        <div class="option">
          <ul>
              <li
                v-for="item in ourQuizs[actions.quizIndex].options"
                :key="item.value"
                v-html="item.label"
                @click="actions.selectFn(item)"
                :class="item.value==ourQuizs[actions.quizIndex].yourAnswer?'selected':''">
              </li>
          </ul>
        </div>

        <hr/>

        <!-- direction control -->
        <div class="row justify-content-center">
          <div class="col-auto">
            <button
              type="button"
              class="btn btn-primary"
              :disabled="!actions.quizIndex"
              @click="actions.prevFn"
            >
              <i class="bi bi-chevron-left"></i>
            </button>
          </div>
          <div class="col-auto">
            <button
              type="button"
              class="btn btn-primary"
              v-if="actions.quizIndex < 8"
              @click="actions.nextFn"
            >
              <i class="bi bi-chevron-right"></i>
            </button>
          </div>
          <div class="col-auto">
            <button
              type="button"
              class="btn btn-primary"
              v-if="actions.quizIndex == 8"
              data-bs-toggle="modal"
              data-bs-target="#resultModal"
              :disabled="!actions.checkSelection()"
              @click="actions.showResult"
              :loading="actions.loading"> 
              {{actions.loading?"Retrieving...":"Result"}}
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- result modal -->
    <!-- <div v-if="model.visible" class="answer-model-shadow"> -->
    <div class="modal fade" tabindex="-1" aria-labelledby="resultModal" aria-hidden="true" id="resultModal">
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div class="modal-header" style="border: none">
            <h1 class="modal-title fs-5"> Result </h1>
          </div>
          <div class="modal-body" style="border: none">
            <div>
              There are a total of <b>{{ result.total }}</b>
              questions, you answered <b>{{ result.corrent }}</b> 
              of them correctly with an <br /> accuracy rate of <b>{{(result.corrent*100/result.total).toFixed(0)}}%</b>.
            </div>
            <div>
              <div v-for="item in result.lists" :key="item.type">
                <b>For {{item.name}} scam types:</b> <b>{{ item.total }}</b> questions in total, <b>{{ item.correctTotal }}</b> answered correctly. </div>
            </div>
            <div>
              <b>Conclusion: </b>
              <span v-if="result.lists.filter(item=>!item.isGood).map(item=>item.name).length">You made more mistakes in the <a href="/learning"><span class="danger">{{result.lists.filter(item=>!item.isGood).map(item=>item.name).join(" and ")}}</span></a> scam category, It's recommended that you study more about this topic </span>
              <span v-if="result.lists.filter(item=>!item.isGood).map(item=>item.name).length&&result.lists.filter(item=>item.isGood).map(item=>item.name).length">,</span>
              <span v-if="result.lists.filter(item=>item.isGood).map(item=>item.name).length">You performed perfectly in the <a href="/report"><span class="success">{{result.lists.filter(item=>item.isGood).map(item=>item.name).join(" and ")}}</span></a> categories, please keep it up.</span>
            </div>
          </div>
          <div class="modal-footer justify-content-center" style="border: none">
            <div class="row justify-content-center mt-2">
              <div class="col-auto">
                <button
                  type="button"
                  class="btn btn-primary"
                  style="border-radius: 25px;"
                  data-bs-dismiss="modal"
                  aria-label="Close"
                  @click="model.jumpTo('/quiz')"
                >
                  Close
                </button>
              </div>
              <div class="col-auto">
                <button
                  type="button"
                  class="btn btn-primary"
                  style="border-radius: 25px;"
                  @click="model.jumpTo('/scan')"
                >
                  Check For Spam
                </button>
              </div>
            </div>
          </div>
          <!-- <a-button type="primary" @click="model.hideModal">Close</a-button>
          <a-button type="primary" @click="model.jumpTo('/scan')"> Scan </a-button> -->
        </div>
      </div>
    </div>
  <!-- </a-modal> -->
  </div>
  <div class="answer-model-shadow" v-if="showTipModel">
    <div class="answer-model">
      <div class="model-title">
        Quiz Intro
      </div>
      <div class="description">Scamscan is dedicated to promoting online safety. Our specially designed quizzes help you gauge your information security awareness. By participating, you can identify areas to improve and ensure you're well-prepared against cyber threats. Why not take our quiz now and check your readiness?</div>
      <div class="desc-lists">
        <!-- <div class="desc-h3">Purpose:</div> -->
        <div class="desc-p"><b>Purpose: </b>Scamscan promotes online safety awareness.</div>
      </div>
      <div class="desc-lists">
        <!-- <div class="desc-h3">Verify source of information:</div> -->
        <div class="desc-p"><b>Number of Questions: </b>9.</div>
      </div>
      <div class="desc-lists">
        <div class="desc-p">
          <p><b>Key Topics Covered:</b></p>
            <ul>
              <li>Phishing</li>
              <li>Spoofing</li>
              <li>Card Theft</li>
            </ul>
        </div>
      </div>
      <div class="desc-lists">
        <div class="desc-p">
          <p><b>Quiz Benefits:</b></p>
            <ul>
              <li>Assess current knowledge.</li>
              <li>Identifv learning areas.</li>
              <li>Enhance readiness against online threats.</li>
            </ul>
        </div>
      </div>
      <div class="btn-group1">
        <button class="btn btn-primary" @click="hideTipModelFn" type="primary">Test Your Knowledge</button>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { reactive, ref, onMounted } from 'vue';
  import { quizs, getRandomElementsFromArray } from "../utils/index"
  import { message } from 'ant-design-vue';
  import axios from "axios";
  const radioStyle = reactive({
    display: 'flex',
    height: '30px',
    lineHeight: '30px',
  });
  const types = [3, 3, 3];
  const max = types.reduce((prev,next)=>prev+next);
  //const typeMaps = ["Email", "Phishing", "SMS", "Elder"];
  const typeMaps = ["Phishing", "Spoofing", "Card theft"];
  const ourQuizs = ref([{}]);
  const result = reactive({
    lists: [],
    total: max,
    corrent: 0,
  });

  const actions = reactive({
    loading:false,
    quizIndex:0,

    checkSelection:()=>{
      const yourAnswer = ourQuizs.value[actions.quizIndex].yourAnswer;
      return yourAnswer;
    },

    prevFn:()=>{
      actions.quizIndex--;
      if(actions.quizIndex===ourQuizs.length){
        return;
      }
    },

    nextFn:()=>{
      const yourAnswer = actions.checkSelection();
      if(!yourAnswer){
        message.warning('Please select the answer!');
        return;
      }
      actions.quizIndex++;
      if(actions.quizIndex===0){
        return;
      }
    },

    selectFn:(item) => {
      ourQuizs.value[actions.quizIndex].yourAnswer = item.value;
    },

    showResult:()=>{
      const yourAnswer = actions.checkSelection();
      if(!yourAnswer){
        message.warning('Please select the answer!');
        return;
      }
      result.corrent = 0
      result.lists = types.map((item,index)=>{
        result.corrent += ourQuizs.value.filter(item=>item.type==(index+1)).filter(item=>item.correct==item.yourAnswer).length
        return {
          type:index+1,
          name:typeMaps[index],
          total: item,
          correctTotal: ourQuizs.value.filter(item=>item.type==(index+1)).filter(item=>item.correct==item.yourAnswer).length,
          isGood:item == ourQuizs.value.filter(item=>item.type==(index+1)).filter(item=>item.correct==item.yourAnswer).length
        }
      })
      actions.loading=true
      setTimeout(()=>{
        // model.showModal()
        actions.loading=false
      },2000)
    }
  });
  const showTipModel = ref(true);
  const hideTipModelFn = ()=>{
    showTipModel.value = false;
  }
  const model = reactive({
    visible:false,
    jumpTo:(url)=>{
      window.location.href= url
    },

    // showModal:()=>{
    //   model.visible = true;
    // },
    // hideModal:()=>{
    //   model.visible = false;
    //   result.lists = [];
    //   result.total = max;
    //   result.corrent = 0;
    //   actions.quizIndex = 0;
    //   ourQuizs.value.forEach(item=>{
    //     item.yourAnswer = ""
    //   })
    // }
  })

  onMounted(() => {
    axios.get('/api/quiz/getquizpool')
    .then(function (response) {
      if(response.data.code==200){
        const lists=response.data.data
        lists.forEach(item=>{
          item.yourAnswer="";
          // item.options = JSON.parse(item.options);
          item.options = JSON.parse(item.options);
        })
        ourQuizs.value = []
        types.forEach((type, index)=>{
          const myTypeItem = lists.filter(item=>item.type==(index+1))
          const selects = getRandomElementsFromArray(myTypeItem, type)
          ourQuizs.value.push(...selects);
        })
        ourQuizs.value.sort((a,b)=>{
          return a.id-b.id
        })
      }else{
        ourQuizs.value=[]
      }
    })
  })
</script>

<style scoped>
  .quiz{
    background-color: #efefef;
    height: calc(100vh - 0px);
    padding-top:8rem;
  }
  .quiz-wrapper{
    display: flex;
    margin-left: 2rem;
    margin-right: 2rem;
    flex-direction: column;
    background-color: #fff;
    padding:24px 30px;
    border-radius: 8px;
  }

  .quiz-wrapper .progress{
    text-align: center;
    margin-bottom: 20px;
    font-size: 30px;
    letter-spacing: 1px;
  }

  .title{
    font-size: 24px;
    line-height: 36px;
  }
  .option {
    margin-top: 20px;
    align-self: flex-start;
  }
  .option ul,.option li{
    list-style: none;
    margin:0;
    padding:0;
  }
  .option li {
    font-size: 16px;
    line-height: 20px;
    cursor: pointer;
    margin: 12px 0;
    padding: 8px;
    border-radius: 8px;
    min-width: 200px;
    border: 1px solid #d9d9d9;
    background-color: rgba(0, 0, 0, 0.06);
  }
  .option li.selected{
    background-color: #ffd84c;
    color: #fff;
  }
  .direction{
    margin-top: 50px;
    display: flex;
  }
  .direction .prev{
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .direction .next{
    margin-left: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .direction .result{
    margin-left: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .selection{
    width: 1000px;
    margin: 30px auto 0;
    display: flex;
    justify-content: center;
  }
  .selection button{
    margin-left: 30px;
    width: 140px;
  }
  .selection button:nth-child(1){
    margin-left: 0;
    background-color: #58e442;
  }
  .selection button:nth-child(1):hover{
    background-color: #4cc03a;
  }
  .selection button:nth-child(2){
    background-color: #ca4e46;
  }
  .selection button:nth-child(2):hover{
    background-color: #ac443c;
  }
  .selection button:nth-child(3){
    background-color: #9b7a36;
  }
  .selection button:nth-child(3):hover{
    background-color: #7e632d;
  }
  .selection button:nth-child(4){
    background-color: #469aca;
  }
  .selection button:nth-child(4):hover{
    background-color: #3b80a8;
  }

  .answer-model-shadow{
    width: 100%;
    height: 100%;
    position: fixed;
    left:0;
    top: 0;
    right: 0;
    bottom: 0;
    z-index: 999;
    background-color: rgba(0, 0, 0, 0.4);
  }
  .answer-model .model-title{
    font-size: 24px;
    color: #dcb732;
    font-weight: bold;
  }
  /* .answer-model .ant-modal-title{
    font-size: 28px;
    font-weight: 600;
    color: #222C28;
  }
   */
  .answer-model{
    background-color: #fff;
    min-width: 300px;
    min-height: 350px;
    border-radius: 4px;
    z-index: 1000;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    padding: 30px;
    color: #000;
    font-size: 16px;
    line-height: 24px;
  }
  .answer-model-shadow .description{
    margin:12px 0;
  }
  .desc-lists {
    margin-bottom:12px;
  }
  .desc-lists .desc-h3{
    font-weight: bold;
  }
  .answer-model .model-title{
    font-size: 24px;
    color: #dcb732;
    font-weight: bold;
  }
  .answer-model .btn-group{
    padding-top: 30px;
    display: flex;

  }
  .answer-model .btn-group button{
    margin: 0 auto;
    display: block;
    width: 140px;
    letter-spacing: 1px;
    background-color: #fbce2f;
  }
  .answer-model .btn-group button:hover{
    background-color: #dcb732;
  }
  .answer-model .btn-group1{
    padding-top: 30px;
    display: flex;

  }
  .answer-model .btn-group1 button{
    margin: 0 auto;
    display: block;
    width: 250px;
    letter-spacing: 1px;
    background-color: #fbce2f;
  }
  .answer-model .btn-group1 button:hover{
    background-color: #dcb732;
  }
  .model-answer-content{
    font-size: 16px;
    margin-top: 12px;
    margin-bottom: 8px;
    color: #222C28;
  }
  .model-lists .model-list{
    line-height: 28px;
    color: #222C28;
    font-size: 14px;
  }
  .model-conclusion{
    margin-top: 12px;
    line-height: 28px;
    color: #222C28;
    font-size: 16px;
  }
  .model-conclusion .success{
    color: #20A675;
  }
  .model-conclusion .danger{
    color: #ff4d4f;
  }

</style>
<style>
:where(.css-dev-only-do-not-override-1ugq3w3).ant-progress .ant-progress-text{
  display: none;
}
:where(.css-dev-only-do-not-override-1ugq3w3).ant-progress .ant-progress-success-bg, :where(.css-dev-only-do-not-override-1ugq3w3).ant-progress .ant-progress-bg{
  background-color: #ffd84c;
}
:where(.css-dev-only-do-not-override-1ugq3w3).ant-btn-primary{
  background-color: #ffd84c;
}
:where(.css-dev-only-do-not-override-1ugq3w3).ant-btn-primary:not(:disabled):hover{
  background-color: #e7c444;
}
</style>