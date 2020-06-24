<template>
  <v-layout row>
    <v-layout column class="questions">
      <v-flex xs6 sm2 md2 lg2>
        <span class="headline">Search by tag: &nbsp;</span>
        <span class="display-1">{{ $route.params.tag }}</span>
      </v-flex>
      <QuestionsList></QuestionsList>
    </v-layout>
    <!-- <WatchedTags v-if="$store.state.isLogin" class="watchedPanel"></WatchedTags> -->
  </v-layout>
</template>
<script>
import http from '@/api/http';
import swal from 'sweetalert2';
import QuestionsList from '@/components/QuestionsList.vue';

export default {
  components: {
    QuestionsList,
  },
  mounted() {
    this.getQuestionsByTag();
  },
  watch: {
    $route(newv, oldv) {
      console.log('old..', oldv);
      console.log('new tag ...', newv);
      this.getQuestionsByTag(newv.params.tag);
    },
  },
  methods: {
    getQuestionsByTag(selectedTag) {
      const { tag } = this.$route.params;

      if (selectedTag && tag) {
        http
          .get(`/questions/tagged/${tag.toLowerCase()}`)
          .then(({ data }) => {
            this.$store.commit('setQuestions', data);
          })
          .catch((err) => {
            let msg = err;
            if (err.response) {
              msg = err.response.data;
            }
            swal.fire('Error', msg, 'error');
          });
      }
    },
  },
};
</script>
<style scoped>
.questions {
    max-width: 700px;
    width: 80%;
}
</style>
