<template>
  <div class="mainWrapper">
    <div class="wrap">
      <aside class="leftAside"></aside>
      <section class="wrap_section">
        <div class="burger">
          <button>
            <img src="./assets/svg/burger.svg" alt="burgerImg">
          </button>
          <h1 class="title">Проведение ТО и мелкий ремонт</h1>
        </div>
        <div class="links">
          <div class="links_leftGroup">
            <a href="#">Общее</a>
            <a class="links_leftGroup__secondLink" href="#">Товары</a>
            <a href="#">Доп. расходы</a>
          </div>
          <div class="links_rightGroup">
            <button class="buttonMenu">
              <img src="./assets/svg/combined-shape.svg" alt="settingsIcon">
            </button>
          </div>
        </div>
        <div class="newRow">
          <button class="adderRow" @click="addNewRow"><span class="plus">+</span> Добавить строку</button>
        </div>
        <div class="wrapTable">
          <div class="redactTable">
            <div>
              <button class="buttonsWithoutBorder" @click="sendDataToServer">Сохранить изменения</button>
            </div>
            <div class="links_rightGroup">
              <button @click="showListMenu" class="buttonMenu"><img src="./assets/svg/combined-shape.svg"
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
          <AgGridVue class="ag-theme-quartz" style="min-width: max-content;" :rowData="rowData"
            :columnDefs="listMenu[0].listColumns" :rowDragManaged=true :onGridReady="onGridReady"
            :enableCellChangeFlash=true @visibleChanged="onRowDataUpdated" @RowDragEnd="onRowDragEnd">
          </AgGridVue>
          <div class="mobileBlock">
            <div v-for="(item, idx) in rowData" :key="item.id" class="mobileBlockItem "
              @dragstart="onDragStart($event, item.id)" draggable="true" @drop="onDrop($event, item.id)"
              @dragenter="onDragEnter($event, item.id)" @dragover.prevent ref="rows">
              <div class="mobileBlockNumber ">
                <h6 class="">Номер строки</h6>
                <button class="burgerButton ">
                  <img src="./assets/svg/burger.svg" alt="burgerImg">
                </button>
                {{ idx + 1 }}
              </div>
              <div class="mobileBlockDoing mobileBlockNumber " @click="handlerWindowDoing(item)">
                <h6>Действие</h6>
                <button class="burgerButton burgerButton_colorBlue ">
                  <img src="./assets/svg/Combined Shape2.svg" alt="burgerImg">
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
          <div class="wrapResult">
            <div class="wrapResult_right">
              <div class="preResult">
                <div class="rowForSumm">
                  <div>
                    Сумма:
                  </div>
                  <div>
                    {{ summCosts }}
                  </div>
                </div>
                <div class="rowForSumm">
                  <div>
                    Кол-во:
                  </div>
                  <div>
                    {{ summPoints }}
                  </div>
                </div>
                <div class="rowForSumm">
                  <div>
                    Общий вес:
                  </div>
                  <div>
                    {{ summWeights }}
                  </div>
                </div>
              </div>
              <div class="preResult">
                <div class="rowForSumm summ">
                  <div>
                    Общая сумма:
                  </div>
                  <div>
                    {{ summCosts }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>  
 
<script>
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-quartz.css";
import axios from 'axios';
import { AgGridVue } from "ag-grid-vue3";
export default {
  name: 'App',
  data() {
    return {
      listMenu: [{
        name: 'Отображение столбцов ',
        listColumns: []
      },
      { name: "Порядок столбцов ", showListColumns: false, listColumns: [] }
      ],
      showList: false,
      showListColumns: false,
      rowData: null,
      options: ["Мраморный щебень фр. 30 мм, 40кг",
        "Мраморный щебень фр. 2-5 мм, 25кг",
        "Мраморный щебень фр. 4-8 мм, 10кг",
        "Мраморный щебень фр. 3-10 мм, 15кг"],
    }
  },
  components: {
    AgGridVue
  },
  methods: {
    addNewRow() {
      this.rowData.push({
        id: this.rowData.length + 1, index: "", " ": "", "Наименование единицы": this.options[0],
        "Цена": 1231, "Кол-во": 12, "Название товара": "Мраморный щебень", "Итого": "", doing: false
      })
      this.rowData = [...this.rowData]
    },
    onGridReady(params) {
      this.gridApi = params.api;
    },
    showListMenu() {
      this.showList = !this.showList
    },
    showListNewColumns(item) {
      item.showListColumns = !item.showListColumns
    },
    onRowDataUpdated(field) {
      const columnDef = this.listMenu[0].listColumns.find((def) => def.field == field);
      columnDef.hide = !columnDef.hide;
      this.gridApi.setColumnDefs(this.listMenu[0].listColumns);
    },
    onRowDragEnd() {
      const columnDef = this.listMenu[0].listColumns.map((e, i) => e.index = i)
      this.gridApi.setColumnDefs([...this.listMenu[0].listColumns, columnDef]);
    },
    sendDataToServer() {
      const data = { key: 'value' }

      axios.post('.....', data)
        .then(response => {
          console.log(response.data)
        })
        .catch(error => {
          console.error(error);
        });
    },
    getSummCost(item) {
      return item["Цена"] * item["Кол-во"]
    },
    handlerWindowDoing(item) {
      item.doing = !item.doing
      return this.rowData = [...this.rowData]
    },
    delRow(id) {
      const index = this.rowData.findIndex(e => e.id === id)
      this.rowData.splice(index, 1)
      return this.rowData = [...this.rowData]
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
    }
  },
  computed: {

    summCosts() {
      if (this.rowData) {
        return this.rowData.reduce((sum, el) => sum + el["Кол-во"] * el["Цена"], 0)
      }
      return 0
    },
    summPoints() {
      if (this.rowData) {
        return this.rowData.reduce((sum, el) => sum + el["Кол-во"], 0)
      }
      return 0
    },
    summWeights() {
      if (this.rowData) {
        return this.rowData.reduce((sum, el) =>
          sum + (+el["Наименование единицы"].match(/\d+кг/i)[0].match(/\d+/i)) *
          el["Кол-во"], 0)
      }
      return 0
    },
  },
  mounted() {
    const mq = window.matchMedia('(max-width: 600px)');
    mq.addEventListener('change', () => this.rowData = [...this.rowData]);

    this.listMenu[0].listColumns = [
      {
        field: 'Индекс',
        hide: false,
        headerName: '',
        width: 50,
        editable: false,
        valueGetter: params => params.node.rowIndex + 1,
      },
      {
        field: "Перемещение",
        hide: false,
        headerName: '',
        rowDrag: true,
        width: 50

      },
      {
        field: "Наименование единицы",
        editable: true,
        filter: false,
        sortable: false,
        cellEditor: 'agSelectCellEditor',
        flex: 2.1,
        hide: false,
        singleClickEdit: true,
        onCellValueChanged: (params) => {
          switch (params.data["Наименование единицы"]) {
            case this.options[0]:
              this.rowData[params.node.rowIndex]["Цена"] = 1231
              break
            case this.options[1]:
              this.rowData[params.node.rowIndex]["Цена"] = 1500
              break
            case this.options[2]:
              this.rowData[params.node.rowIndex]["Цена"] = 1000
              break
            case this.options[3]:
              this.rowData[params.node.rowIndex]["Цена"] = 860
              break
          }
          return this.rowData = [...this.rowData]
        },
        valueGetter: params => params.data["Наименование единицы"],
        cellEditorParams: {
          values:
            [this.options[0], this.options[1], this.options[2], this.options[3]]
        },
      },
      {
        field: "Цена", editable: true, cellEditor: 'agTextCellEditor',
        singleClickEdit: true, hide: false, flex: 1
      },
      {
        field: "Кол-во", editable: true, cellEditor: 'agTextCellEditor',
        singleClickEdit: true, hide: false, flex: 1
      },
      {
        field: "Название товара",
        editable: true,
        cellEditor: 'agTextCellEditor',
        flex: 1.6,
        hide: false,
      },
      {
        field: "Итого",
        flex: 1,
        hide: false,
        valueGetter: (params) => params.data["Итого"] = params.data["Цена"] * params.data["Кол-во"]
      },
    ]
    this.rowData = [
      {
        id: 1, index: "", " ": "", "Наименование единицы": this.options[0],
        "Цена": 1231, "Кол-во": 12, "Название товара": "Мраморный щебень", "Итого": "", doing: false
      },
      {
        id: 2, index: "", " ": "", "Наименование единицы": this.options[1],
        "Цена": 1500, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "", doing: false
      },
      {
        id: 3, index: "", " ": "", "Наименование единицы": this.options[2],
        "Цена": 1000, "Кол-во": 5, "Название товара": "Мраморный щебень", "Итого": "", doing: false
      },
      {
        id: 4, index: "", " ": "", "Наименование единицы": this.options[3],
        "Цена": 860, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "", doing: false
      },
    ]
  },
}  
</script>  
 
<style lang="scss">
@import "./Styles.scss";
</style>  
