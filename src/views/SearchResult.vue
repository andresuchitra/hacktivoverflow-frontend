<template>
  <v-layout row>
    <v-layout column class="questions">
      <v-flex xs6 sm2 md2 lg2>
        <span class="headline">Search: &nbsp;</span>
        <span class="display-1">{{ $route.params.key }}</span>
      </v-flex>
      <QuestionsList></QuestionsList>
    </v-layout>
    <!-- <WatchedTags v-if="$store.state.isLogin" class="watchedPanel"></WatchedTags> -->
  </v-layout>
</template>
<script>
import http from '@/api/http';
import QuestionsList from '@/components/QuestionsList.vue';
import swal from 'sweetalert2';

export default {
  components: {
    QuestionsList,
  },
  mounted() {
    this.getQuestionsByTag();
  },
  watch: {
    $route(newv) {
      this.searchQuestions(newv.params.key);
    },
  },
  methods: {
    searchQuestions(selected) {
      const { key } = this.$route.params;
      if (selected && key) {
        http
          .get(`/questions/search/${key.toLowerCase()}`)
          .then(({ data }) => {
            this.$store.commit('setQuestions', data);
          })
          .catch((err) => {
            let msg = '';
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
