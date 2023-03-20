<template>
    <score-table title="Lower Section">
        <rule-box :rule="threeOfAKind" @scoring="scoring"/>
        <rule-box :rule="fourOfAKind" @scoring="scoring"/>
        <rule-box :rule="fullHouse" @scoring="scoring"/>
        <rule-box :rule="smallStraight" @scoring="scoring"/>
        <rule-box :rule="largeStraight" @scoring="scoring"/>
        <rule-box :rule="chance" @scoring="scoring"/>
        <rule-box :rule="yahtzee" @scoring="scoring"/>
    </score-table>
</template>

<script>
import ScoreTable from "@/components/ScoreTable.vue";
import RuleBox from "@/components/RuleBox.vue";

const EMIT_KEYS = {
    SCORING: "scoring",
}

export default {
    name: "LowerSection",
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
            threeOfAKind: {
                name: "Three Of A Kind",
                possiblePoint: 0,
                score: 0,
            },
            fourOfAKind: {
                name: "Four Of A Kind",
                possiblePoint: 0,
                score: 0,
            },
            fullHouse: {
                name: "Full House",
                point: 35,
                possiblePoint: 0,
                score: 0,
            },
            smallStraight: {
                name: "Small Straight",
                consecutiveNumber: 3,
                point: 30,
                possiblePoint: 0,
                score: 0,
            },
            largeStraight: {
                name: "Large Straight",
                consecutiveNumber: 4,
                point: 40,
                possiblePoint: 0,
                score: 0,
            },
            chance: {
                name: "Chance",
                possiblePoint: 0,
                score: 0,
            },
            yahtzee: {
                name: "Yahtzee",
                point: 50,
                count: 0,
                possiblePoint: 0,
                score: 0,
            },
            doubleYahtzee: {
                name: "Double Yahtzee",
                point: 100,
            }
        }
    },
    computed: {
        totalScore() {
            return this.threeOfAKind.score
                + this.fourOfAKind.score
                + this.fullHouse.score
                + this.smallStraight.score
                + this.largeStraight.score
                + this.chance.score
                + this.yahtzee.score
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
            this.threeOfAKind.possiblePoint = this.calculateNumberOfAKind(3)
            this.fourOfAKind.possiblePoint = this.calculateNumberOfAKind(4)
            this.fullHouse.possiblePoint = this.calculateFullHouse()
            this.smallStraight.possiblePoint = this.calculateStraight(this.smallStraight)
            this.largeStraight.possiblePoint = this.calculateStraight(this.largeStraight)
            this.chance.possiblePoint = this.sumOfAllDice()
            this.yahtzee.possiblePoint = this.calculateYahtzee()

            if (this.yahtzee.possiblePoint > 0 && this.yahtzee.count === 1) {
                this.yahtzee.score += this.doubleYahtzee.point
                this.scoring(this.yahtzee)
            }
        },
        calculateNumberOfAKind(number) {
            const sameDiceMap = this.getSameDiceMap()

            const overNumberOfACount = Object.keys(sameDiceMap)
                .filter(key => sameDiceMap[key] >= number).length > 0

            if (!overNumberOfACount) {
                return 0
            }

            return this.sumOfAllDice()
        },
        calculateFullHouse() {
            let two = false;
            let three = false;
            const sameDiceMap = this.getSameDiceMap()

            Object.keys(sameDiceMap)
                .forEach(key => {
                    if (sameDiceMap[key] === 2) {
                        two = true
                    }

                    if (sameDiceMap[key] === 3) {
                        three = true
                    }
                })

            if (two && three) {
                return this.fullHouse.point;
            }

            return 0;
        },
        calculateStraight(rule) {
            const sortedArray = this.diceList
                .map(dice => dice.value)
                .sort()

            let previousValue = sortedArray[0];
            let consecutiveCount = 0;
            for (let index = 1; index < 5; index++) {
                let currentValue = sortedArray[index];

                const diff = currentValue - previousValue

                if (diff === 1) {
                    consecutiveCount++
                    if (consecutiveCount >= rule.consecutiveNumber) {
                        return rule.point
                    }
                } else if (diff > 1) {
                    consecutiveCount = 0
                }

                previousValue = currentValue
            }

            return 0
        },
        sumOfAllDice() {
            return this.diceList
                .map(dice => dice.value)
                .reduce((sum, value) => sum + value, 0);
        },
        calculateYahtzee() {
            const isYahtzee = this.diceList
                .map(dice => dice.value)
                .every((value, index, array) => value === array[0])

            if (isYahtzee) {
                return this.yahtzee.point
            }

            return 0
        },
        getSameDiceMap() {
            return this.diceList
                .map(dice => dice.value)
                .reduce((result, value) => {
                    result[value] = (result[value] || 0) + 1
                    return result
                }, {})
        },
        scoring(rule) {
            if (rule.name === this.yahtzee.name) {
                this.yahtzee.count++
            }

            this.$emit(EMIT_KEYS.SCORING, this.totalScore)
        },
    },
}
</script>

<style scoped>

</style>