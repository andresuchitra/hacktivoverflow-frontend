<template>
    <v-layout column>
        <v-flex mb-2>
            <a id="title" v-bind:to="'/questions/'+$route.params.id">{{latestQuestion.title}}</a>
        </v-flex>
        <v-flex>
            <ItemDetail 
                :entity="latestQuestion" 
                v-on:upvote="doUpvoteQ"
                v-on:downvote="doDownvoteQ"
                v-on:edit="isEditing = true"
                v-on:delete="deleteQuestion"
                ></ItemDetail>
        </v-flex>
        <v-flex v-if="isEditing">
            <h2>Edit Question</h2>
            <QuestionForm mt-5 class="qform"
                :type="'edit'"
                :question="$store.state.question"
                v-on:submit="updateQuestion" 
                v-on:cancel="isEditing = false"
                >
            </QuestionForm>
        </v-flex>
        <v-divider></v-divider>
        <v-flex v-if="latestQuestion.answers && latestQuestion.answers.length > 0" my-3>
            <h2>Answers</h2>
        </v-flex>
        <v-flex v-for="(answer,j) in latestQuestion.answers" :key="j">
            <ItemDetail 
                :entity="answer"
                v-on:upvote="doUpvote"
                v-on:downvote="doDownvote"
                v-on:edit="isEditingAnswer = true"
                v-on:delete="deleteAnswer"
                ></ItemDetail>
            <v-flex v-if="isEditingAnswer">
                <h2>Edit Answer</h2>
                <AnswerForm mt-5 class="qform"
                    :type="'edit'"
                    :initanswer="answer"
                    v-on:submit="editAnswer" 
                    v-on:cancel="isEditing = false"
                    >
                </AnswerForm>
            </v-flex>
        </v-flex>
        <v-divider></v-divider>
        <AnswerForm v-on:answerSubmitted="refreshQuestionPage"></AnswerForm>
    </v-layout>
</template>

<script>
import ItemDetail from '@/components/ItemDetail.vue'
import AnswerForm from '@/components/AnswerForm.vue'
import QuestionForm from '@/components/QuestionForm.vue'

import {mapState} from 'vuex'

export default {
    name: 'QuestionDetailsView',
    components: {
        ItemDetail,
        AnswerForm,
        QuestionForm,
    },
    data() {
        return {
            isEditing: false,
            isEditingAnswer: false,
        }
    },
    mounted() {
        this.$store.dispatch('getQuestionDetail', this.$route.params.id)
    },
    methods: {
        updateQuestion(data) {
            this.$store.dispatch('editQuestion', data.question)
            this.isEditing = false
        },
        doUpvoteQ() {
            if(this.$store.state.isLogin) {
                this.$store.dispatch('upVoteQuestion', this.latestQuestion._id)
            }
            else this.$router.push('/login')
        },
        doDownvoteQ() {
            if(this.$store.state.isLogin) {
                this.$store.dispatch('downVoteQuestion', this.latestQuestion._id)
            }
            else this.$router.push('/login')
        },
        doUpvote(data) {
            if(this.$store.state.isLogin) {
                this.$store.dispatch('upVoteAnswer', data)
            }
            else this.$router.push('/login')
        },
        doDownvote(data) {
            if(this.$store.state.isLogin) {
                this.$store.dispatch('downVoteAnswer', data)
            }
            else this.$router.push('/login')
        },
        refreshQuestionPage: function() {
            // this.$router.go();
            this.$store.dispatch('getQuestionDetail', this.$route.params.id)
        },
        deleteQuestion(id) {
            this.$store.dispatch('deleteQuestion', this.$route.params.id)
            .then(({ data }) => {
                console.log('delete question...', data);
                this.$store.dispatch('getQuestions');
                this.$router.push('/questions')
            })
            .catch((err) => {
                console.log('error delete question');
                this.$store.commit('setError', err.response);
            });
        },
        editAnswer(answer) {
            //todo
            this.$store.dispatch('editAnswer',answer)
            this.isEditingAnswer = false
        },
        deleteAnswer(id) {
            this.$store.dispatch('deleteAnswer', id)
        },
    },
    computed: {
        refreshQuestion: () =>{
            return questionRefreshed
        },
        latestQuestion() {
            return this.$store.state.question
        },
    },

    watch: {
        '$route' (to, from) {
            this.$store.dispatch('getQuestionDetail', this.$route.params.id)
            this.isEditing = false;
        },
        latestQuestion(newQuestion, oldQuestion) {
            console.log('newQ..=> ', newQuestion);
            console.log('oldQ..=> ', oldQuestion);
        },
    },

}
</script>

<style scoped>
#title {
    font-size: 2rem;
}
.qform {
    padding: 10px ;
    width: 100%;
    border: 2px dashed lightgray;
}
</style>
