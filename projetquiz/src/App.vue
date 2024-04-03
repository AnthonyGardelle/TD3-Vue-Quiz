<template>
  <div>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <div class="card bg-light mb-3">
      <h1>Les Questionnaires</h1>
      <div class="card" v-for="questionnaire in questionnaires.questionnaires" :key="questionnaire.id">
        <QuestionnaireItem :questionnaire="questionnaire" @remove="removeQuestionnaire" @modify="editQuestionnaire"
          @consult="consultQuestions" @removeQ="removeQuestion" @addQ="addQuestion"/>
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
        fetch(`http://127.0.0.1:5000/quiz/api/v1.0/questionnaires/${payload.id}`, {
          method: 'DELETE'
        }).then(response => response.json())
          .then(data => {
            console.log('Success:', data);
          })
          .catch((error) => {
            console.error('Error:', error);
          });
      }
    },
    editQuestionnaire(payload) {
      let newName = prompt("Entrez le nouveau nom du questionnaire");
      let index = this.questionnaires.questionnaires.findIndex(questionnaire => questionnaire.id === payload.id);
      if (index !== -1 && newName !== null && newName !== "" && newName.trim() !== "") {
        this.questionnaires.questionnaires[index].name = newName;
        fetch(`http://127.0.0.1:5000/quiz/api/v1.0/questionnaires/${payload.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            name: newName
          })
        }).then(response => response.json())
          .then(data => {
            console.log('Success:', data);
          })
          .catch((error) => {
            console.error('Error:', error);
          });
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
      let index = this.questionnaires.questionnaires.findIndex(questionnaire => questionnaire.id === payload.id);
      let questionIndex = this.questionnaires.questionnaires[index].questions.findIndex(question => question.id === payload.questionId);
      if (questionIndex !== -1) {
        this.questionnaires.questionnaires[index].questions.splice(questionIndex, 1);
        fetch(`http://127.0.0.1:5000/quiz/api/v1.0/questions/${payload.questionId}`, {
          method: 'DELETE'
        }).then(response => response.json())
          .then(data => {
            console.log('Success:', data);
          })
          .catch((error) => {
            console.error('Error:', error);
          });
      }
    },
    addQuestion(payload) {
      let div = document.getElementById(`ajouterQuestion${payload.id}`);
      let bouton = document.getElementById("addQ-" + payload.id);
      let radioSimple = document.getElementById("simple" + payload.id);
      let radiomultiple = document.getElementById("multiple" + payload.id);
      radioSimple.addEventListener("click", function() {
        document.getElementById("choix3").style.display = "none";
        document.getElementById("choix4").style.display = "none";
      });
      radiomultiple.addEventListener("click", function() {
        document.getElementById("choix3").style.display = "block";
        document.getElementById("choix4").style.display = "block";
      });  
      if (div.style.display === "none") {
        div.style.display = "block";
        bouton.value = "Fermer";
      } else {
        div.style.display = "none";
        bouton.value = "Ajouter Question";
      }
    }
  }
}
</script>