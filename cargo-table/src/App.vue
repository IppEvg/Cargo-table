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
          <RedactTable :listMenu="listMenu" @showListMenu="showListMenu"></RedactTable>
          <AgGridVue class="ag-theme-quartz" style="min-width: max-content;" :rowData="rowData"
            :columnDefs="listMenu[0].listColumns" :rowDragManaged="true" :onGridReady="onGridReady" :editable="true"
            :enableCellChangeFlash="true" @visibleChanged="onRowDataUpdated" @RowDragEnd="onRowDragEnd">
            <!-- // подавляет анимацию перемещения строк и делает линию между строк, куда вставиться перемещеамая строка// :suppressMoveWhenRowDragging=true>  -->
          </AgGridVue>
          <MobileBlock :rowData="rowData" :options="options" @delRow="delRow"></MobileBlock>
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
import MobileBlock from "./components/MobileBlock.vue"
import RedactTable from "./components/RedactTable.vue"
export default {
  name: 'App',
  data() {
    return {
      listMenu: [{
        name: 'Отображение столбцов ',
        showListColumns: false,
        listColumns: []
      },
      { name: "Порядок столбцов ", showListColumns: false, listColumns: [] }
      ],
      rowData: null,
      options: ["Мраморный щебень фр. 30 мм, 40кг",
        "Мраморный щебень фр. 2-5 мм, 25кг",
        "Мраморный щебень фр. 4-8 мм, 10кг",
        "Мраморный щебень фр. 3-10 мм, 15кг"],
    }
  },
  components: {
    AgGridVue, MobileBlock, RedactTable
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
    delRow(id) {
      const index = this.rowData.findIndex(e => e.id === id)
      this.rowData.splice(index, 1)
      return this.rowData = [...this.rowData]
    },

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
        editable: false,
        headerName: '',
        rowDrag: true,
        width: 50,
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
      {
        field: "Действие",
        flex: 0.8,
        cellRenderer: (params) => {
          let button = document.createElement("button")
          button.innerHTML = 'Del'
          button.classList.add("buttonOfRows", "adderRow")
          button.addEventListener("click", () => this.delRow(params.data.id))
          return button
        }
      }
    ]
    this.rowData = [
      {
        id: 1, index: "", " ": "", "Наименование единицы": this.options[0],
        "Цена": 1231, "Кол-во": 12, "Название товара": "Мраморный щебень", "Итого": "", doing: false,
      },
      {
        id: 2, index: "", " ": "", "Наименование единицы": this.options[1],
        "Цена": 1500, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "", doing: false,
      },
      {
        id: 3, index: "", " ": "", "Наименование единицы": this.options[2],
        "Цена": 1000, "Кол-во": 5, "Название товара": "Мраморный щебень", "Итого": "", doing: false,
      },
      {
        id: 4, index: "", " ": "", "Наименование единицы": this.options[3],
        "Цена": 860, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "", doing: false,
      },
    ]
  },
}  
</script>  
 
<style lang="scss">
@import "./Styles.scss";
</style>  
