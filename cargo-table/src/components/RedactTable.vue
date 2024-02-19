<template>
    <div class="redactTable">
        <div>
            <button class="buttonsWithoutBorder" @click="sendDataToServer">Сохранить изменения</button>
        </div>
        <div class="links_rightGroup">
            <button @click="() => showList = !showList" class="buttonMenu"><img src="../assets/svg/combined-shape.svg"
                    alt="settingsIcon">
            </button>
            <div v-show="showList" class="listOptions">
                <button class="buttonColumnMenu" v-for="item, index in listMenu" :key="index">
                    <div class="wrapButtonColumnMenu">
                        <button @click="showListNewColumns(item)" class="buttonColumnMenu">
                            {{ item.name }}
                            <div v-if="!item.showListColumns">&gt;</div>
                            <div v-else> &lt;</div>
                        </button>
                        <div v-if="item.showListColumns" class="listColumn">
                            <div v-for="column in item.listColumns" :key="column.field">
                                <input type="checkbox" id="idx" @change="onRowDataUpdated(column.field)" checked="true" />
                                &nbsp;
                                <label for=" idx">{{ column.field }}</label>
                            </div>
                        </div>
                    </div>
                </button>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "RedactTable",
    data() {
        return {
            showList: false
        }
    },
    props: ['listMenu'],
    emits: [],
    methods: {

    },
    mounted() {

    }
}
</script>