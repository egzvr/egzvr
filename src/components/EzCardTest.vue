<template>
  <div class="w-full h-screen">
    <div class="flex flex-col h-full">
      <div
        class="h-4/5 bg-gray-100 flex justify-center items-center"
        data-testid="cards"
      >
        <div class="w-1/3 h-auto">
          <div
            v-if="state === 'DRAFT'"
            class="w-full shadow flex flex-col bg-white rounded-lg p-6 text-center"
          >
            <textarea v-model="rawList" class="w-full border"></textarea>

            <button
              class="w-full mt-5 bg-green-500 text-white rounded"
              @click="onParseRawList"
            >
              PREPARE DATA
            </button>
          </div>
          <div
            v-if="state === 'AWAIT_START'"
            class="w-full shadow bg-white rounded-lg p-6 text-center"
          >
            <button class="" @click="state = 'TESTING'">START</button>
          </div>
          <template v-else-if="state === 'TESTING'">
            <div
              v-if="cardSide === 'QUESTION'"
              class="w-full shadow bg-white rounded-lg p-6 text-center"
            >
              {{ allCards[currentCardNumber][0] }}
            </div>
            <div
              v-if="cardSide === 'ANSWER'"
              class="w-full shadow bg-white rounded-lg p-6 text-center"
            >
              {{ allCards[currentCardNumber][1] }}
            </div>
          </template>
          <div
            v-else-if="state === 'TOTAL_SCORE'"
            class="w-full flex flex-wrap justify-center shadow bg-white rounded-lg p-6 text-center"
          >
            <div class="w-full">THE END</div>
            <table class="w-full mt-5">
              <thead>
                <tr>
                  <th>Wrong</th>
                  <th>Right</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td>
                    <ul>
                      <li
                        v-for="card in wrongAnswersList"
                        :key="card[0] + 'FinalList'"
                      >
                        {{ card[0] }}
                      </li>
                    </ul>
                  </td>
                  <td>
                    <ul>
                      <li
                        v-for="card in rightAnswersList"
                        :key="card[0] + 'FinalList'"
                      >
                        {{ card[0] }}
                      </li>
                    </ul>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
      <div
        class="h-1/5 bg-gray-200 flex flex-col justify-center"
        data-testid="controls"
      >
        <div v-if="state === 'TESTING'" class="w-full h-20 flex justify-evenly">
          <button
            class="w-1/6 h-16 text-white"
            :class="{
              'bg-red-500': cardSide === 'QUESTION',
              'bg-gray-500': cardSide === 'ANSWER',
            }"
            :disabled="cardSide === 'ANSWER'"
            @click="onWrongAnswer"
          >
            X {{ wrongTotal }}
          </button>
          <button
            class="w-1/6 h-16 text-white"
            :class="{
              'bg-green-500': cardSide === 'QUESTION',
              'bg-gray-500': cardSide === 'ANSWER',
            }"
            :disabled="cardSide === 'ANSWER'"
            @click="onRightAnswer"
          >
            V {{ rightTotal }}
          </button>
          <button
            class="w-1/6 h-16 text-white"
            :class="{
              'bg-gray-500': cardSide === 'QUESTION',
              'bg-green-500': cardSide === 'ANSWER',
            }"
            :disabled="cardSide === 'QUESTION'"
            @click="onNext"
          >
            Next
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "EzCardTest",
  data() {
    return {
      rawList: "",
      state: "DRAFT",
      cardSide: "QUESTION",
      allCards: [
        ["dog", "собака"],
        ["cat", "кошка"],
        ["elephant", "слон"],
        ["octopus", "осьминог"],
        ["rabbit", "кролик"],
      ],
      currentCardNumber: 0,
      rightAnswersIds: [],
      wrongAnswersIds: [],
    };
  },
  methods: {
    onParseRawList() {
      this.allCards = this.rawList.split(/\r?\n/).map((row) => {
        return row.split("|");
      });

      this.state = "AWAIT_START";
    },
    onRightAnswer() {
      this.rightAnswersIds.push(this.currentCardNumber);
      this.flipToAnswerSide();
    },
    onWrongAnswer() {
      this.flipToAnswerSide();
      this.wrongAnswersIds.push(this.currentCardNumber);
    },
    onNext() {
      if (this.currentCardNumber < this.allCards.length - 1) {
        this.currentCardNumber++;
        this.flipToQuestionSide();
      } else {
        this.state = "TOTAL_SCORE";
      }
    },
    flipToAnswerSide() {
      this.cardSide = "ANSWER";
    },
    flipToQuestionSide() {
      this.cardSide = "QUESTION";
    },
  },
  computed: {
    rightAnswersList() {
      return this.allCards.filter((card, index) =>
        this.rightAnswersIds.includes(index)
      );
    },
    wrongAnswersList() {
      return this.allCards.filter((card, index) =>
        this.wrongAnswersIds.includes(index)
      );
    },
    rightTotal() {
      return this.rightAnswersIds.length;
    },
    wrongTotal() {
      return this.wrongAnswersIds.length;
    },
  },
};
</script>
