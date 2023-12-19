<template>
    <div class="profile">
        <div>
            <div class="head">
                <div class="header">
                    Profile
                </div>
                <div v-show="hasValidProfile" @click="$emit('setView', 'main')">
                    <img class="close-profile" src="../assets/svg/close.svg" alt="">
                </div>
            </div>
            <div class="content">
                <div class="field">
                    <label for="name">Name</label>
                    <input type="text" name="" id="name" v-model="profile.name">
                </div>
                <div class="field">
                    <label for="rank">Rank</label>
                    <select name="" id="rank" v-model="profile.rank">
                        <option value="rp">Regular Pioneer</option>
                        <option value="ap">Auxillary Pioneer</option>
                    </select>
                </div>
            </div>
        </div>
        <div class="reset">
            <button @click="$emit('storeReset')">Reset All</button>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        profile: Object,
    },
    computed: {
        hasValidProfile() {
            return this.profile.name && this.profile.rank
        }
    }
}
</script>

<style scoped>

.profile {
    display: flex;
    flex-flow: column;
    height: 85dvh;
    justify-content: space-between;
    padding-bottom: 50px;
}
.content
{
    padding-top: 50px;
    display: flex;
    flex-flow: column;
    gap: 30px;
}

.field
{
    display: flex;
    flex-flow: column;

}

.field label
{
    text-align: left;
    color: gray;
}

.field input,
.field select
{
    border: none;
    padding: 15px 10px;
    font-size: 22px;
    font-family: 'Poppins', sans-serif;
    border-bottom: 1px solid gray;
    outline: none;
    background: transparent;
    width: 100%;
    box-sizing: border-box;
}

.close-profile
{
    height: 30px;
}

.reset button
{
    background: #720b0b;
    color: white;
    border-radius: 5px;
    width: 80%;
    outline: none;
}
</style>