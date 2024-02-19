<template>
  <div class="mainWrapper">
    <div class="wrap">
      <aside class="leftAside"></aside>
      <section class="wrap_section">
        <BurgerMenu></BurgerMenu>
        <LinksMenu></LinksMenu>
        <NewRowAdder @addNewRow="addNewRow"></NewRowAdder>
        <div class="wrapTable">
          <RedactTable :listMenu="listMenu" @sendDataToServer="sendDataToServer" @onGridReady="onGridReady" @onRowDataShower="onRowDataShower" ></RedactTable>
          <AgGridVue class="ag-theme-quartz" style="min-width: max-content;" :rowData="rowData"
            :columnDefs="listMenu[0].listColumns" :suppressRowTransform= "true" :rowDragManaged="true" :onGridReady="onGridReady" :editable="true"
            :enableCellChangeFlash="true" @visibleChanged="onRowDataShower" @RowDragEnd="onRowDragEnd" @cell-value-changed="onCellValueChanged" >
            <!-- // подавляет анимацию перемещения строк и делает линию между строк, куда вставиться перемещеамая строка// :suppressMoveWhenRowDragging=true>  -->
          </AgGridVue>
          <MobileBlock :rowData="rowData" :options="options" @delRow="delRow"></MobileBlock>
          <ResultBlock :rowData="rowData"></ResultBlock>
        </div>
      </section>
    </div>
  </div>
</template>  
 
<script>
import "ag-grid-community/styles/ag-grid.css"
import "ag-grid-community/styles/ag-theme-quartz.css"
import axios from 'axios'
import { AgGridVue } from "ag-grid-vue3"
import MobileBlock from "./components/MobileBlock.vue"
import RedactTable from "./components/RedactTable.vue"
import NewRowAdder from "./components/NewRowAdder.vue"
import LinksMenu from "./components/LinksMenu.vue"
import BurgerMenu from "./components/BurgerMenu.vue"
import ResultBlock from "./components/ResultBlock.vue"
export default {
  name: 'App',
  data() {
    return {
      listMenu: [
        {name: 'Отображение столбцов ',
        listColumns: []},
        { name: "Порядок столбцов ", 
        showListColumns: false, listColumns: [] }
      ],
      rowData: null,
      options: ["Мраморный щебень фр. 30 мм, 40кг",
        "Мраморный щебень фр. 2-5 мм, 25кг",
        "Мраморный щебень фр. 4-8 мм, 10кг",
        "Мраморный щебень фр. 3-10 мм, 15кг"],
    }
  },
  components: {
    AgGridVue, MobileBlock, RedactTable,NewRowAdder,LinksMenu,BurgerMenu,ResultBlock
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
    onRowDataShower(field) {
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
    onCellValueChanged(){
this.rowData=[...this.rowData]
    }
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
        flex: 2,
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
        field: "Цена", editable: true, cellEditor: 'agNumberCellEditor',
        singleClickEdit: true, hide: false, flex: 1
      },
      {
        field: "Кол-во", editable: true, cellEditor: 'agNumberCellEditor',
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
        flex: 1,
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
  }
}  
</script>  
 
<style lang="scss">
@import "./Styles.scss";
</style>  
