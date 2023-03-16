<template>
    <div>
        {{ rule.name }} :
        <button v-if="!complete" @click="scoring">+{{ rule.possiblePoint }}</button>
        <span v-else>{{ rule.score }}</span><br/>
    </div>
</template>

<script>
const EMIT_KEYS = {
    SCORING: "scoring",
}

export default {
    name: "RuleBox",
    props: {
        rule: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            complete: false,
        }
    },
    methods: {
        scoring() {
            this.rule.score = this.rule.possiblePoint
            this.complete = true

            this.$emit(EMIT_KEYS.SCORING, this.rule)
        },
    },
}
</script>

<style scoped>

</style>