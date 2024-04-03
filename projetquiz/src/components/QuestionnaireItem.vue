<template>
    <div class="d-flex flex-column">
        <h3>{{ questionnaire.name }}</h3>
        <div :class="questionnaire.name" style="display: none;">
            <div v-for="question in questionnaire.questions" :key="question.id" :id="`${question.id}`">
                <p>{{ question.title }}</p>
                <div v-if="question.question_type === 'simple'" class="d-flex justify-content-around">
                    <label>{{ question.question.choix1 }}</label>
                    <input type="radio" :name="`question-${question.id}`" :value="question.question.choix1">
                    <input type="radio" :name="`question-${question.id}`" :value="question.question.choix2">
                    <label>{{ question.question.choix2 }}</label>
                    <input type="button" class="btn btn-danger" value="Supprimer" @click="supprQ(question.id)">
                </div>
                <div v-else-if="question.question_type === 'multiple'" class="d-flex justify-content-around">
                    <div v-for="(choice, index) in question.question" :key="index">
                        <label>{{ choice }}</label>
                        <input type="checkbox" :name="`question-${question.id}`" :value="choice">
                    </div>
                    <input type="button" class="btn btn-danger" value="Supprimer" @click="supprQ(question.id)">
                </div>
            </div>
            <input type="button" class="btn btn-primary" value="Ajouter Question" @click="addQ"
                :id="`addQ-${questionnaire.id}`">
            <div :id="`ajouterQuestion-${questionnaire.id}`" style="display: none;">
                <form action="">
                    <input type="radio" name="typeQ" :id="``" checked>
                    <label for="simple">Simple</label>
                    <input type="radio" name="typeQ" id="multiple">
                    <label for="multiple">Multiple</label>
                </form>
                <div class="d-flex flex-wrap justify-content-center"
                    style="width: 10%; margin-left: auto; margin-right: auto;">
                    <input type="text" id="questionTitle" placeholder="Question">
                    <input type="text" id="choix1" placeholder="Choix 1">
                    <input type="text" id="choix2" placeholder="Choix 2">
                    <input type="text" id="choix3" placeholder="Choix 3">
                    <input type="text" id="choix4" placeholder="Choix 4">
                </div>
            </div>
        </div>
        <div class="d-flex flex-row mt-2 justify-content-around">
            <input type="button" class="btn btn-danger" value="Supprimer" @click="suppr">
            <input type="button" class="btn btn-warning" value="Modifier" @click="modif">
            <input type="button" class="btn btn-primary" value="Consulter" @click="consult"
                :id="`consult-${questionnaire.id}`">
        </div>
    </div>
</template>

<script>
export default {
    props: {
        questionnaire: Object
    },
    data() {
        return {
            selectedChoices: {}
        };
    },
    methods: {
        suppr() {
            this.$emit('remove', { id: this.questionnaire.id });
        },
        modif() {
            this.$emit('modify', { id: this.questionnaire.id });
        },
        consult() {
            this.$emit('consult', { id: this.questionnaire.id });
        },
        supprQ(questionId) {
            this.$emit('removeQ', { id: this.questionnaire.id, questionId: questionId });
        },
        addQ() {
            this.$emit('addQ', { id: this.questionnaire.id });
        }
    },
    emits: ['remove', 'modify', 'consult', 'removeQ', "addQ"]
}
</script>
