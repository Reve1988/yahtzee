<template>
    <div>
        <div>
            <button @click="rollDice" :disabled="isNoRemainRollCount() || isNoDiceSelected()">
                Roll selected dice ({{ remainRollCount }}/{{ rollCount }})
            </button>
        </div>
        <div>
            <div v-for="(dice, index) in diceList" class="dice" :class="{'vibration': dice.rolling}">
                <input :id="'dice' + index" type="checkbox" v-model="dice.selected"/>
                <label :for="'dice' + index">{{ dice.value }}</label>
            </div>
        </div>
    </div>
</template>

<script>
const ROLL_COUNT = 3

const EMIT_KEYS = {
    ROLL: "roll",
}

export default {
    name: "DiceBox",
    props: {
        turnCount: {
            type: Number,
            required: true,
        }
    },
    data() {
        return {
            rollCount: ROLL_COUNT,
            remainRollCount: ROLL_COUNT,
            diceList: [
                {id: 0, value: 0, selected: true, rolling: false},
                {id: 1, value: 0, selected: true, rolling: false},
                {id: 2, value: 0, selected: true, rolling: false},
                {id: 3, value: 0, selected: true, rolling: false},
                {id: 4, value: 0, selected: true, rolling: false},
            ],
        }
    },
    watch: {
        turnCount() {
            this.initRollCount()
            this.selectAllDice()
            this.rollDice()
        },
    },
    mounted() {
        this.rollDice()
    },
    methods: {
        initRollCount() {
            this.remainRollCount = ROLL_COUNT
        },
        selectAllDice() {
            this.diceList.forEach(dice => dice.selected = true)
        },
        isNoRemainRollCount() {
            return this.remainRollCount === 0
        },
        isNoDiceSelected() {
            return this.diceList.filter(dice => dice.selected).length === 0
        },
        rollDice() {
            this.diceList
                .filter(dice => dice.selected)
                .forEach(dice => {
                    dice.rolling = true
                    setTimeout(() => dice.rolling = false, 400)
                })

            this.remainRollCount--

            this.diceList
                .filter(dice => dice.selected)
                .forEach(dice => {
                    dice.value = this.getRandomDiceNumber()
                    dice.selected = false
                })

            this.$emit(EMIT_KEYS.ROLL, JSON.parse(JSON.stringify(this.diceList)))
        },
        getRandomDiceNumber() {
            return Math.floor(Math.random() * 6) + 1;
        },
    },
}
</script>

<style scoped>

</style>