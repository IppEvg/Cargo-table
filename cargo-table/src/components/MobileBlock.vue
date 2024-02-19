<template>
    <div class="mobileBlock">
        <div v-for="(item, idx) in rowData" :key="item.id" class="mobileBlockItem "
            @dragstart="onDragStart($event, item.id)" draggable="true" @drop="onDrop($event, item.id)"
            @dragenter="onDragEnter($event, item.id)" @dragover.prevent ref="rows">
            <div class="mobileBlockNumber ">
                <h6 class="">Номер строки</h6>
                <button class="burgerButton ">
                    <img src="../assets/svg/burger.svg" alt="burgerImg">
                </button>
                {{ idx + 1 }}
            </div>
            <div class="mobileBlockDoing mobileBlockNumber " @click="handlerWindowDoing(item)">
                <h6>Действие</h6>
                <button class="burgerButton burgerButton_colorBlue ">
                    <img src="../assets/svg/Combined Shape2.svg" alt="burgerImg">
                </button>
                <div v-show="item.doing" class="windowDoing ">
                    <button @click="delRow(item.id)">Удалить</button>
                </div>
            </div>
            <div class="mobileBlockRow ">
                <h6>Наименование единицы</h6>
                <select v-model="item['Наименование единицы']" class="mobileBlockRow_select">
                    <option v-for="option, index in options" :key="index"
                        :selected="option === item['Наименование единицы'] ? true : false">
                        {{ option }}
                    </option>
                </select>
            </div>
            <div class="mobileBlockRow ">
                <h6>Цена</h6>
                <input type="number" v-model="item['Цена']" class="mobileBlockRow_select" />
            </div>
            <div class="mobileBlockRow">
                <h6>Кол-во</h6>
                <input type="number" v-model="item['Кол-во']" class="mobileBlockRow_select " />
            </div>
            <div class="mobileBlockRow ">
                <h6>Наименование товара</h6>
                <input type="text" :value="item['Название товара']" readonly class=" mobileBlockRow_select" />
            </div>
            <div class="mobileBlockRow ">
                <h6>Итого</h6>
                <input type="number" :value="getSummCost(item)" readonly class=" mobileBlockRow_select" />
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'MobileBlock',
    props: [
        "rowData", "options"
    ],
    emits: ['delRow'],
    methods: {
        getSummCost(item) {
            return item["Цена"] * item["Кол-во"]
        },
        onDragEnter(e) {
            e.preventDefault()
            if (e.target.classList.value === "mobileBlockItem") {
                this.$refs.rows.forEach(e => {
                    if (e.classList == "mobileBlockItem active") {
                        e.classList.remove("active")
                    }
                })
                e.target.classList.add("active")
            }
            let children = e.target.children;
            for (var i = 0; i < children.length; i++) {
                return
            }
        },
        onDrop(e, id) {
            this.$refs.rows.forEach(e => {
                if (e.classList == "mobileBlockItem active") {
                    e.classList.remove("active")
                }
            })
            const itemFirstIdx = this.rowData.findIndex(el => el.id == e.dataTransfer.getData("itemId"))
            const itemSecondIdx = this.rowData.findIndex(el => el.id === id);
            [this.rowData[itemFirstIdx], this.rowData[itemSecondIdx]] = [this.rowData[itemSecondIdx], this.rowData[itemFirstIdx]]
        },
        onDragStart(e, id) {
            e.dataTransfer.drpEffect = "move"
            e.dataTransfer.effectAllowed = "move"
            e.dataTransfer.setData('itemId', id)
        },
        handlerWindowDoing(item) {
            item.doing = !item.doing
        },
        delRow(id) {
            this.$emit("delRow", id)
        }
    },
    mounted() {
        return this.rowData
    },
    updated() {
        return this.rowData
    }
}
</script>