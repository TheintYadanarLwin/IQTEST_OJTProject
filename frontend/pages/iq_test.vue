<template>
  <div>
    <!--Logobar header-->
    <client-only class="bg-dark">
      <b-nav tabs fill class="b-color">
        <button class="btn btn-danger next-btn">
          <NuxtLink to="/" class="next-route text-white">Exit</NuxtLink>
        </button>
        <!--Iqtest content-->
        <b-nav-item
          tag="nuxt-link"
          class="btn-secondary text-white"
          v-for="(item, index) in data"
          :key="item.id"
          :to="`/iq_test/${item.id}`"
          exact
          exact-active-class="active"
        >
          {{ index + 1 }}. {{ item.title }}
        </b-nav-item>
      </b-nav>
    </client-only>
    <nuxt-child :data="data" />

    <!--Footer-->
    <div
      class="b-color footer bg-secondary footer navbar-fixed-bottom"
      id="footer"
    >
      <div class="bottom-50 end-10">
        <span class="ans-count text-white font-weight-bolder font-italic"
          >{{ choiceAns.choosen }} / {{ choiceAns.total }}
        </span>

        <span
          class="ans-count1 text-warning font-weight-bolder"
          v-show="!finish && !oneleft"
          >{{ choiceAns.left }} questions are left.
        </span>
        <span class="ans-count1 text-danger font-weight-bolder" v-show="oneleft"
          >{{ choiceAns.left }} question is still left.
        </span>

        <span
          class="ans-count1 text-success font-weight-bolder"
          v-show="finish"
        >
          All Done!
        </span>

        <!--Next Session-->
        <div v-if="nextRoute">
          <button class="btn btn-light next-btn">
            <NuxtLink
              :to="nextRoute"
              class="l-0 next-route text-white font-weight-bold"
              >➡️</NuxtLink
            >
          </button>
        </div>
        <div v-else id="btn-next" v-show="finish">
          <button
            class="btn btn-success font-weight-bolder text-center mr-5 mb-5"
            @click="submitAns()"
          >
            Submit
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
  
  <script>
import * as _ from "lodash";
import { mapState } from "vuex";
export default {
  async asyncData({ $axios, store, redirect }) {
    let data;
    if (process.client) {
      data = store.state.languages;
    } else {
      redirect("/");
    }

    return { data };
  },
  computed: {
    ...mapState(["answers"]),
/*Next Session*/
    nextRoute() {
      const id = this.$route.params.id;
      const index = this.$store.state.languages.findIndex(
        (lan) => lan.id == id
      );
      if (index + 1 === this.$store.state.languages.length) {
        return null;
      }
      const nextId = this.$store.state.languages[index + 1].id;
      return `/iq_test/${nextId}`;
    },

    choiceAns() {
      let total = 0;
      let choosen = 0;
      let left = 0;
      this.$store.state.answers.map((ans) => {
        total += ans.answers ? ans.answers.length : 0;
        choosen += ans.answers ? ans.answers.filter((a) => a !== "").length : 0;
        left = total - choosen;
      });
      return { total, choosen, left };
    },
    /*Remaining Question status conditions*/
    finish() {
      let total = 0;
      let choosen = 0;

      this.$store.state.answers.map((ans) => {
        total += ans.answers ? ans.answers.length : 0;
        choosen += ans.answers ? ans.answers.filter((a) => a !== "").length : 0;
      });
      return total - choosen === 0;
    },
    oneleft() {
      let total = 0;
      let choosen = 0;

      this.$store.state.answers.map((ans) => {
        total += ans.answers ? ans.answers.length : 0;
        choosen += ans.answers ? ans.answers.filter((a) => a !== "").length : 0;
      });
      return total - choosen === 1;
    },
  },

  methods: {
    /*Save Answer to Database*/
    async submitAns() {
      let choose_ans = [];

      this.answers.map((ans) => {
        if (ans.answers) {
          choose_ans = choose_ans.concat(ans.answers.filter((a) => a !== ""));
        }
      });
      try {
        const res = await this.$axios.post("/api/v1/iq_test", {
          answer: choose_ans.join(","),
        });
        if (res) {
          this.$router.push("/");
        }
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
  <style scoped>
.nav-tabs .nav-link.active {
  color: #fff;
  background-color: #ffc107;
}
a {
  color: #00ff47;
}
.ans-count {
  margin-left: 50%;
}
.ans-count1 {
  margin-left: 70%;
  margin-bottom: 15px;
}
.btn-light {
  margin-left: 80%;
}
.btn-success {
  margin-left: 80%;
}

</style>
  