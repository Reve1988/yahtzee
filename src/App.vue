<template>
    <div class="warp">
        <div class="dice-box">
            <dice-box :turn-count="turnCount" @roll="setDice"/>
        </div>
        <div class="score-box">
            <score-table>
                <tr>
                    <th>Turn</th>
                    <td>{{ turnCount }} / {{ totalTurnCount }}</td>
                </tr>
            </score-table>
            <upper-section :dice-list="diceList" @scoring="upperScoring"/>
            <lower-section :dice-list="diceList" @scoring="lowerScoring"/>
            <score-table>
                <tr>
                    <th>Total Score</th>
                    <td>{{ totalScore }}</td>
                </tr>
            </score-table>
            <button v-if="turnCount === totalTurnCount" @click="refresh">다시하기</button>
        </div>
    </div>
</template>

<script>
import DiceBox from "@/components/DiceBox.vue";
import UpperSection from "@/components/UpperSection.vue";
import LowerSection from "@/components/LowerSection.vue";
import ScoreTable from "@/components/ScoreTable.vue";

export default {
    name: "YahtzeeApp",
    components: {
        DiceBox,
        UpperSection,
        LowerSection,
        ScoreTable,
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
