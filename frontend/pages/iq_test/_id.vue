<template>
  <!--Iq Test-->
  <div v-if="childData" class="fadeIn">
    <div v-for="(d, i) in childData.children" :key="d.id">
      <b-card>
        <span> Question No - {{ i + 1 }}</span>
        <br />
        <h6 class="ml-5 mt-3">{{ d.title }}</h6>
        <div v-for="cd in d.children" :key="cd.id" class="ml-5">
          <b-form-radio
            class="text-bold"
            :value="cd.id"
            v-model="answers[currentLanIndex].answers[i]"
            @change="
              $store.commit('UPDATE_ANSWER', {
                p_index: currentLanIndex,
                c_index: i,
                value: cd.id,
              })
            "
          >
            {{ cd.title }}
          </b-form-radio>
        </div>
      </b-card>
    </div>
  </div>
</template>
  <script>
import * as _ from "lodash";
export default {
  props: ["data"],
  data() {
    return {
      answers: [],
    };
  },
  asyncData({ store }) {
    if (process.client) {
      return { answers: _.cloneDeep(store.state.answers) };
    }
  },
  created() {
    this.answers = _.cloneDeep(this.$store.state.answers);
  },
  computed: {
    childData() {
      return this.data.find((d) => d.id == this.$route.params.id);
    },
    currentLanIndex() {
      return this.$store.state.answers.findIndex(
        (ans) => ans.language_id == this.$route.params.id
      );
    },
  },
};
</script>
  <<style scoped>
  /* Simple CSS3 Fade-in Animation */
@-webkit-keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@-moz-keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.fadeIn {
  opacity: 0;
  -webkit-animation: fadeIn ease-in 1;
  -moz-animation: fadeIn ease-in 1;
  animation: fadeIn ease-in 1;

  -webkit-animation-fill-mode: forwards;
  -moz-animation-fill-mode: forwards;
  animation-fill-mode: forwards;

  -webkit-animation-duration: 1s;
  -moz-animation-duration: 1s;
  animation-duration: 1s;
}


  </style>