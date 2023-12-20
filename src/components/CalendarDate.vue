<template>
    <div class="hrs plan" v-show="showPlan">
        {{ day.p }}
    </div>
    <div class="hrs actual">
        {{ day.a }}
    </div>
</template>

<script>
export default {
    props: {
        day: Object
    },
    computed: {
        showPlan() {
            return this.day.p > 0 && (this.day.a == 0 || this.day.a == '') 
        }
    }
}
</script>

<style scoped>
.hrs
{
    font-size: 22px;
    font-weight: 600;
    text-align: center;

    display: flex;
    justify-content: center;
    align-items: center;
}

.plan
{
    flex: 0;
    color: rgb(158, 158, 158);
    height: fit-content;
}

.actual
{
    flex: 0;
    color: rgb(1, 1, 136);
}
</style>