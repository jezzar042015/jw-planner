<template>
    <div class="hrs plan" v-html="htmlPlan" v-show="showPlan" >
    </div>
    <div class="hrs actual" v-html="htmlActual">
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
        },
        htmlPlan() {
            return this.day.p + ((this.day.p) ? `<span style='font-weight: 400; font-size: 14px'>h</span>` : '')
        },
        htmlActual() {
            return this.day.a + ( this.day.a ? `<span style='font-weight: 400; font-size: 14px'>h</span>` : '')
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

.hrs-h {
    font-weight: 200;
    color: red;
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