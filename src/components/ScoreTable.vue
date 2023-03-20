<template>
    <table class="score-table">
        <colgroup>
            <col style="width: 300px"/>
            <col style="width: 300px"/>
        </colgroup>
        <tbody>
        <tr v-if="title !== undefined">
            <th colspan="2" class="score-table-title">{{ title }}</th>
        </tr>
        <slot/>
        </tbody>
    </table>
</template>

<script>
export default {
    name: "ScoreTable",
    props: {
        title: {
            type: String,
            required: false,
            default: undefined,
        }
    }
}
</script>
