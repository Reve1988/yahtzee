<template>
    <div v-for="dice in state.dices">
        <input type="checkbox" v-model="dice.selected"/>
        <span>{{ dice.value }}</span>
    </div>
    <br/>
    <button @click="runTurn" :disabled="state.remainRollCount === 0">다시굴리기({{ state.remainRollCount }}/3)</button>
    <br/>
    <br/>
    Aces :
    <button v-if="!state.aces.complete" @click="choice(state.aces)">+{{ state.aces.possiblePoint }}</button>
    <span v-else>{{ state.aces.point }}</span><br/>
    Tows :
    <button v-if="!state.tows.complete" @click="choice(state.tows)">+{{ state.tows.possiblePoint }}</button>
    <span v-else>{{ state.tows.point }}</span><br/>
    Threes :
    <button v-if="!state.threes.complete" @click="choice(state.threes)">+{{ state.threes.possiblePoint }}</button>
    <span v-else>{{ state.threes.point }}</span><br/>
    Fours :
    <button v-if="!state.fours.complete" @click="choice(state.fours)">+{{ state.fours.possiblePoint }}</button>
    <span v-else>{{ state.fours.point }}</span><br/>
    Fives :
    <button v-if="!state.fives.complete" @click="choice(state.fives)">+{{ state.fives.possiblePoint }}</button>
    <span v-else>{{ state.fives.point }}</span><br/>
    Sixes :
    <button v-if="!state.sixes.complete" @click="choice(state.sixes)">+{{ state.sixes.possiblePoint }}</button>
    <span v-else>{{ state.sixes.point }}</span><br/>
    Bonus : {{ state.bonus }} (-{{ state.remainBonus }})<br/>
    <br/>
    <br/>
    Three Of A Kind :
    <button v-if="!state.threeOfACount.complete" @click="choice(state.threeOfACount)">+{{ state.threeOfACount.possiblePoint }}</button>
    <span v-else>{{ state.threeOfACount.point }}</span><br/>
    Four Of A Kind :
    <button v-if="!state.fourOfACount.complete" @click="choice(state.fourOfACount)">+{{ state.fourOfACount.possiblePoint }}</button>
    <span v-else>{{ state.fourOfACount.point }}</span><br/>
    Full House :
    <button v-if="!state.fullHouse.complete" @click="choice(state.fullHouse)">+{{ state.fullHouse.possiblePoint }}</button>
    <span v-else>{{ state.fullHouse.point }}</span><br/>
    Small Straight :
    <button v-if="!state.smallStraight.complete" @click="choice(state.smallStraight)">+{{ state.smallStraight.possiblePoint }}</button>
    <span v-else>{{ state.smallStraight.point }}</span><br/>
    Large Straight :
    <button v-if="!state.largeStraight.complete" @click="choice(state.largeStraight)">+{{ state.largeStraight.possiblePoint }}</button>
    <span v-else>{{ state.largeStraight.point }}</span><br/>
    Chance :
    <button v-if="!state.chance.complete" @click="choice(state.chance)">+{{ state.chance.possiblePoint }}</button>
    <span v-else>{{ state.chance.point }}</span><br/>
    Yahtzee :
    <button v-if="!state.yahtzee.complete" @click="choice(state.yahtzee)">+{{ state.yahtzee.possiblePoint }}</button>
    <span v-else>{{ state.yahtzee.point }}</span><br/>
    <br/>
    <br/>
    Point : {{ state.totalPoint }}
</template>

<script>
import {onMounted, reactive} from "vue";

const BONUS = 63

export default {
    name: "dice",
    setup() {
        const state = reactive({
            remainRollCount: 3,
            dices: [
                {index: 0, value: 0, selected: true},
                {index: 1, value: 0, selected: true},
                {index: 2, value: 0, selected: true},
                {index: 3, value: 0, selected: true},
                {index: 4, value: 0, selected: true},
            ],
            aces: {possiblePoint: 0, point: 0, complete: false},
            tows: {possiblePoint: 0, point: 0, complete: false},
            threes: {possiblePoint: 0, point: 0, complete: false},
            fours: {possiblePoint: 0, point: 0, complete: false},
            fives: {possiblePoint: 0, point: 0, complete: false},
            sixes: {possiblePoint: 0, point: 0, complete: false},
            remainBonus: BONUS,
            bonus: 0,
            threeOfACount: {possiblePoint: 0, point: 0, complete: false},
            fourOfACount: {possiblePoint: 0, point: 0, complete: false},
            fullHouse: {possiblePoint: 0, point: 0, complete: false},
            smallStraight: {possiblePoint: 0, point: 0, complete: false},
            largeStraight: {possiblePoint: 0, point: 0, complete: false},
            chance: {possiblePoint: 0, point: 0, complete: false},
            yahtzee: {possiblePoint: 0, point: 0, complete: false},
            totalPoint: 0,
        })

        const getRandomDiceNumber = () => {
            const min = 1;
            const max = 6;
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        const calcUpperSection = (number) => {
            let result = 0;

            for (const dice of state.dices) {
                if (dice.value === number) {
                    result += number;
                }
            }

            return result
        }

        const getSameDiceMap = () => {
            return state.dices
                .map(dice => dice.value)
                .reduce((result, value) => {
                    result[value] = (result[value] || 0) + 1
                    return result
                }, {})
        }

        const calcNumberOfACount = (number) => {
            const sameDiceMap = getSameDiceMap()

            const overThreeOfACount = Object.keys(sameDiceMap)
                .filter(key => sameDiceMap[key] >= number).length > 0

            if (!overThreeOfACount) {
                return 0
            }

            return sumOfAllDice()
        }

        const sumOfAllDice = () => {
            return state.dices
                .map(dice => dice.value)
                .reduce((a, b) => a + b, 0);
        }

        const calcFullHouse = () => {
            let two = false;
            let three = false;
            const sameDiceMap = getSameDiceMap()

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
                return 25;
            }

            return 0;
        }

        const calcStraight = (consecutiveNumber, point) => {
            const sortedArray = state.dices
                .map(dice => dice.value)
                .sort()

            let previousValue = sortedArray[0];
            let consecutiveCount = 0;
            for (let index = 1; index < 5; index++) {
                let currentValue = sortedArray[index];

                const diff = currentValue - previousValue

                if (diff === 1) {
                    consecutiveCount++
                    if (consecutiveCount >= consecutiveNumber) {
                        return point
                    }
                } else if (diff > 1) {
                    consecutiveCount = 0
                }

                previousValue = currentValue
            }

            return 0
        }

        const calcChance = () => {
            return sumOfAllDice();
        }

        const calcYahtzee = () => {
            const firstValue = state.dices[0].value
            for (let index = 1; index < 5; index++) {
                if (firstValue !== state.dices[index].value) {
                    return 0;
                }
            }

            return 50;
        }

        const rollTheDice = () => {
            for (let index = 0; index < 5; index++) {
                if (state.dices[index].selected) {
                    state.dices[index].value = getRandomDiceNumber()
                    state.dices[index].selected = false
                }
            }
        }

        const initTurn = () => {
            state.remainRollCount = 3
            state.dices.forEach(dice => dice.selected = true)
        }

        const calcRemainBonus = () => {
            const totalPoint = state.aces.point
                + state.aces.point
                + state.tows.point
                + state.threes.point
                + state.fours.point
                + state.fives.point
                + state.sixes.point

            return BONUS - totalPoint
        }

        const calcBonus = () => {
            const totalPoint = state.aces.point
                + state.tows.point
                + state.threes.point
                + state.fours.point
                + state.fives.point
                + state.sixes.point

            return totalPoint > BONUS ? 35 : 0
        }

        const calcTotalPoint = () => {
            return state.aces.point
                + state.tows.point
                + state.threes.point
                + state.fours.point
                + state.fives.point
                + state.sixes.point
                + state.bonus
                + state.threeOfACount.point
                + state.fourOfACount.point
                + state.fullHouse.point
                + state.smallStraight.point
                + state.largeStraight.point
                + state.chance.point
                + state.yahtzee.point
        }

        const runTurn = () => {
            if (state.dices.filter(dice => dice.selected).length === 0) {
                return
            }

            state.remainRollCount--

            rollTheDice()

            state.aces.possiblePoint = calcUpperSection(1)
            state.tows.possiblePoint = calcUpperSection(2)
            state.threes.possiblePoint = calcUpperSection(3)
            state.fours.possiblePoint = calcUpperSection(4)
            state.fives.possiblePoint = calcUpperSection(5)
            state.sixes.possiblePoint = calcUpperSection(6)
            state.remainBonus = calcRemainBonus()
            state.bonus = calcBonus()

            state.threeOfACount.possiblePoint = calcNumberOfACount(3);
            state.fourOfACount.possiblePoint = calcNumberOfACount(4);
            state.fullHouse.possiblePoint = calcFullHouse();
            state.smallStraight.possiblePoint = calcStraight(3, 30);
            state.largeStraight.possiblePoint = calcStraight(4, 40);
            state.chance.possiblePoint = calcChance();
            state.yahtzee.possiblePoint = calcYahtzee();

            state.totalPoint = calcTotalPoint();
        }

        const choice = (obj) => {
            obj.point = obj.possiblePoint
            obj.complete = true
            initTurn()
            runTurn()
        }

        onMounted(runTurn)

        return {
            state,
            runTurn,
            choice,
        }
    },
}
</script>

<style scoped>

</style>