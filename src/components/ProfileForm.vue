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

        <div class="actions">
            <div class="backup">
                <button @click="backup">Create Backup</button>
            </div>
            <div class="restore">
                <input type="file" id="fileInput" @change="restore" ref="fileInput" hidden />
                <label for="fileInput" class="button-style">Restore Backup</label>
            </div>
            <div class="reset">
                <button @click="$emit('storeReset')">Reset All</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            backupData: null,
        }
    },
    props: {
        profile: Object,
        months: Array,
    },
    computed: {
        hasValidProfile() {
            return this.profile.name && this.profile.rank
        }
    },
    methods: {
        async backup() {
            // how to make this promise and only execute the rest of the code after this is resolved
            await this.$emit('optimizeStore');

            const data = {
                profile: this.profile,
                months: this.months,
            }
            const filename = 'planner_backup.json'
            const json = JSON.stringify(data, null, 2)
            const blob = new Blob([json], { type: 'application/json' })
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url;
            a.download = filename;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
        },
        async restore(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.readAsText(file, "UTF-8");
                reader.onload = (e) => {
                    try {
                        this.backupData = JSON.parse(e.target.result);
                    } catch (error) {
                        alert("Error parsing JSON file");
                    }
                };
                reader.onerror = () => {
                    alert("Error reading the file");
                };
            }
        },
        async loadBackup() {
            await this.$emit('backup', this.backupData)
            alert("Backup Restored!");
        }
    }, 
    watch: {
        'backupData': {
            handler: 'loadBackup',
            deep: true,
        }
    }
}
</script>

<style scoped>
.profile
{
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

.actions
{
    display: flex;
    gap: 20px;
    flex-flow: column;
}

.backup button,
.restore label,
.reset button
{
    color: white;
    border-radius: 5px;
    width: 80%;
    outline: none;

}

.backup button
{
    background: #333394;
}

.restore label
{
    display: inline-block;
    background: #333394;
    padding: 12px 0;
}

.reset button
{
    background: #720b0b;
}

input[type="file"]
{
    display: none;
}
</style>