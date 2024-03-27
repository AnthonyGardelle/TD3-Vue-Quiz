<template>
	<main class="app">
	  <h1>Module de vue de l'application todo</h1>
	  
	  <section v-if="!selectedQuestionnaire">
		<h2>Choisissez un questionnaire :</h2>
		<ul>
		  <li v-for="questionnaire in questionnaires" :key="questionnaire.id">
			<button @click="selectQuestionnaire(questionnaire.id)">
			  {{ questionnaire.name }}
			</button>
		  </li>
		</ul>
	  </section>
	  
	  <section v-else class="quiz">
		<h2>{{ selectedQuestionnaire.name }}</h2>
		
		<div v-if="questions.length > 0 && !quizCompleted">
		  <div class="quiz-info">
			<span class="question">{{ getCurrentQuestion.question }}</span>
			<span class="score">Score {{ score }}/{{ questions.length }}</span>
		  </div>
		  
		  <div class="options">
			<label 
			  v-for="(option, index) in getCurrentQuestion.options" 
			  :key="index"
			  :for="'option' + index" 
			  :class="`option ${
				getCurrentQuestion.selected === index 
				  ? index === getCurrentQuestion.answer 
					? 'correct' 
					: 'wrong'
				  : ''
			  } ${
				getCurrentQuestion.selected !== null &&
				index !== getCurrentQuestion.selected
				  ? 'disabled'
				  : ''
			  }`">
			  <input 
				type="radio" 
				:id="'option' + index" 
				:name="getCurrentQuestion.index" 
				:value="index" 
				v-model="getCurrentQuestion.selected" 
				:disabled="getCurrentQuestion.selected !== null"
				@change="setAnswer" 
			  />
			  <span>{{ option }}</span>
			</label>
		  </div>
		  
		  <button 
			@click="nextQuestion" 
			:disabled="!getCurrentQuestion.selected">
			{{ 
			  getCurrentQuestion.index === questions.length - 1 
				? 'Finish' 
				: getCurrentQuestion.selected === null
				  ? 'Select an option'
				  : 'Next question'
			}}
		  </button>
		</div>
  
		<div v-else>
		  <h3>You have finished the quiz!</h3>
		  <p>Your score is {{ score }}/{{ questions.length }}</p>
		</div>
	  </section>
	</main>
  </template>
  
  <script setup>
  import { ref, computed, onMounted } from 'vue'
  import axios from 'axios'
  
  const questionnaires = ref([])
  const selectedQuestionnaire = ref(null)
  const questions = ref([])
  const currentQuestion = ref(0)
  const quizCompleted = ref(false)
  
  onMounted(async () => {
	try {
	  const response = await axios.get('http://127.0.0.1:5000/quiz/api/v1.0/questionnaires')
	  questionnaires.value = response.data.questionnaires
	} catch (error) {
	  console.error('Error fetching questionnaires:', error)
	}
  })
  
  const score = computed(() => {
	let value = 0
	questions.value.forEach(q => {
	  if (q.selected !== null && q.answer === q.selected) {
		value++
	  }
	})
	return value
  })
  
  const getCurrentQuestion = computed(() => {
	return questions.value[currentQuestion.value]
  })
  
  const setAnswer = (e) => {
	questions.value[currentQuestion.value].selected = e.target.value
	e.target.value = null
  }
  
  const nextQuestion = () => {
	if (currentQuestion.value < questions.value.length - 1) {
	  currentQuestion.value++
	} else {
	  quizCompleted.value = true
	}
  }
  
  const selectQuestionnaire = async (questionnaireId) => {
	try {
	  const response = await axios.get(`/quiz/api/v1.0/questionnaires/${questionnaireId}`)
	  selectedQuestionnaire.value = response.data
	  questions.value = selectedQuestionnaire.value.questions
	} catch (error) {
	  console.error('Error fetching questionnaire:', error)
	}
  }
  </script>
  
  <style>
  /* Styles go here */
  </style>
  