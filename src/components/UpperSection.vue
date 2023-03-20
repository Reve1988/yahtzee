<template>
    <score-table title="Upper Section">
        <rule-box :rule="ace" @scoring="scoring"/>
        <rule-box :rule="tows" @scoring="scoring"/>
        <rule-box :rule="threes" @scoring="scoring"/>
        <rule-box :rule="fours" @scoring="scoring"/>
        <rule-box :rule="fives" @scoring="scoring"/>
        <rule-box :rule="sixes" @scoring="scoring"/>
        <tr>
            <th>Bonus</th>
            <td>{{ bonus.score }} ({{ bonus.remain }})</td>
        </tr>
    </score-table>
</template>

<script>
import RuleBox from "@/components/RuleBox.vue"
import ScoreTable from "@/components/ScoreTable.vue";

const EMIT_KEYS = {
    SCORING: "scoring",
}

export default {
    name: "UpperSection",
    components: {
        ScoreTable,
        RuleBox,
    },
    props: {
        diceList: {
            type: Array,
            required: true,
        },
    },
    data() {
        return {
            ace: {
                name: "Ace",
                possiblePoint: 0,
                score: 0,
            },
            tows: {
                name: "Tows",
                possiblePoint: 0,
                score: 0,
            },
            threes: {
                name: "Threes",
                possiblePoint: 0,
                score: 0,
            },
            fours: {
                name: "Fours",
                possiblePoint: 0,
                score: 0,
            },
            fives: {
                name: "Fives",
                possiblePoint: 0,
                score: 0,
            },
            sixes: {
                name: "Sixes",
                possiblePoint: 0,
                score: 0,
            },
            bonus: {
                name: "Bonus",
                point: 35,
                needTotalScore: 63,
                remain: 63,
                score: 0,
            },
        }
    },
    computed: {
        totalScore() {
            return this.ace.score
                + this.tows.score
                + this.threes.score
                + this.fours.score
                + this.fives.score
                + this.sixes.score
                + this.bonus.score
        }
    },
    watch: {
        diceList: {
            immediate: true,
            handler() {
                this.calculatePossiblePoints()
            },
        },
    },
    methods: {
        calculatePossiblePoints() {
            this.ace.possiblePoint = this.calculatePossiblePoint(1)
            this.tows.possiblePoint = this.calculatePossiblePoint(2)
            this.threes.possiblePoint = this.calculatePossiblePoint(3)
            this.fours.possiblePoint = this.calculatePossiblePoint(4)
            this.fives.possiblePoint = this.calculatePossiblePoint(5)
            this.sixes.possiblePoint = this.calculatePossiblePoint(6)
        },
        calculatePossiblePoint(number) {
            return this.diceList
                .filter(dice => dice.value === number)
                .reduce((sum, dice) => sum + dice.value, 0)
        },
        scoring() {
            this.bonus.remain = this.bonus.needTotalScore - this.totalScore
            if (this.bonus.remain <= 0) {
                this.bonus.score = this.bonus.point
            }

            this.$emit(EMIT_KEYS.SCORING, this.totalScore)
        },
    },
}
</script>
