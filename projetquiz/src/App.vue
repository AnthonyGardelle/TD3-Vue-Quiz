<template>
  <div>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <div class="card bg-light mb-3">
      <h1>Les Questionnaires</h1>
      <div class="card" v-for="questionnaire in questionnaires.questionnaires" :key="questionnaire.id">
        <QuestionnaireItem :questionnaire="questionnaire" @remove="removeQuestionnaire" @modify="editQuestionnaire"
          @consult="consultQuestions" @remove-q="removeQuestion" />
      </div>
    </div>
  </div>
</template>

<script>
import QuestionnaireItem from './components/QuestionnaireItem.vue';

export default {
  components: {
    QuestionnaireItem
  },
  data() {
    return {
      questionnaires: []
    };
  },
  mounted() {
    this.fetchQuestionnaire();
  },
  methods: {
    fetchQuestionnaire() {
      fetch("http://127.0.0.1:5000/quiz/api/v1.0/questionnaires")
        .then(response => response.json())
        .then(data => {
          this.questionnaires = data;
        })
        .catch(error => {
          console.error('There was an error!', error);
        });
    },
    removeQuestionnaire(payload) {
      const index = this.questionnaires.questionnaires.findIndex(questionnaire => questionnaire.id === payload.id);
      if (index !== -1) {
        this.questionnaires.questionnaires.splice(index, 1);
      }
    },
    editQuestionnaire(payload) {
      let newName = prompt("Entrez le nouveau nom du questionnaire");
      let index = this.questionnaires.questionnaires.findIndex(questionnaire => questionnaire.id === payload.id);
      if (index !== -1 && newName !== null && newName !== "") {
        this.questionnaires.questionnaires[index].name = newName;
      }
    },
    consultQuestions(payload) {
      let index = this.questionnaires.questionnaires.findIndex(questionnaire => questionnaire.id === payload.id);
      let name = this.questionnaires.questionnaires[index].name;
      let div = document.getElementsByClassName(name)[0];
      let bouton = document.getElementById("consult-" + payload.id);
      if (div.style.display === "none") {
        div.style.display = "block";
        bouton.value = "Fermer";
      } else {
        div.style.display = "none";
        bouton.value = "Consulter";
      }
    },
    removeQuestion(payload) {
      console.log(payload.id);
    }
  }
}
</script>