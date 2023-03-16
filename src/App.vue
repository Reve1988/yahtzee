<template>
    Turn : {{ turnCount }} / {{ totalTurnCount }}
    <br/>
    <br/>
    <dice-box :turn-count="turnCount" @roll="setDice"/>
    <br/>
    <br/>
    <upper-section :dice-list="diceList" @scoring="upperScoring"/>
    <br/>
    <lower-section :dice-list="diceList" @scoring="lowerScoring"/>
    <br/>
    Score : {{ totalScore }}
    <button v-if="turnCount === totalTurnCount" @click="refresh">다시하기</button>
</template>

<script>
import DiceBox from "@/components/DiceBox.vue";
import UpperSection from "@/components/UpperSection.vue";
import LowerSection from "@/components/LowerSection.vue";

export default {
    name: "YahtzeeApp",
    components: {
        DiceBox,
        UpperSection,
        LowerSection,
    },
    data() {
        return {
            totalTurnCount: 13,
            turnCount: 0,
            diceList: [],
            upperScore: 0,
            lowerScore: 0,
        }
    },
    computed: {
        totalScore() {
            return this.upperScore + this.lowerScore
        },
    },
    methods: {
        setDice(diceList) {
            this.diceList = diceList
        },
        upperScoring(score) {
            this.turnCount++
            this.upperScore = score
        },
        lowerScoring(score) {
            this.turnCount++
            this.lowerScore = score
        },
        refresh() {
            window.location.reload()
        }
    },
}
</script>

<style scoped>
</style>
