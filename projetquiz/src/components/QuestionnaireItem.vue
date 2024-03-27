<template>
    <div class="d-flex justify-content-around align-items-center">
        <span style="width: 50%;">{{ questionnaire.name }}</span>
        <input type="button" class="btn btn-danger" value="Supprimer" @click="suppr">
        <input type="button" class="btn btn-warning" value="Modifier" @click="modif">
        <input type="button" class="btn btn-primary" value="Consulter" @click="consulter">
    </div>
</template>

<script>
export default {
    props: {
        questionnaire: Object
    },
    methods: {
        suppr() {
            this.$emit('remove', { id: this.questionnaire.id });
        },
        modif() {
            this.$emit('modify', { id: this.questionnaire.id });
        },
        consulter() {
            this.$emit('consult', { id: this.questionnaire.id });
        }
    },
    emits: ['remove', 'modify', 'consult']
}
</script>