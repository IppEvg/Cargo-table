<template>
<div class="wrap">
<aside class="leftAside"></aside>
<section class="wrapSection">
  <h1 class="title">Проведение ТО и мелкий ремонт</h1>
  <div class="links">
    <div class="leftGroup">
      <a href="#">Общее</a>
      <a class="secondLink" href="#">Товары</a>
      <a href="#">Доп. расходы</a>
    </div>
    <div class="rightGroup"><button><img src="./assets/svg/combined-shape.svg" alt="settingsIcon"></button></div>
  </div>
  <div class="newRow">
    <button class="adderRow" @click="addNewCol"><span class="plus">+</span> Добавить строку</button>
  </div>
  <div class="wrapTable">
    <div class="redactTable"><div><button class="buttonsWithoutBorder">Сохранить изменения</button></div> <div class="rightGroup"><button><img src="./assets/svg/combined-shape.svg" alt="settingsIcon"></button></div></div>
    <AgGridVue class="ag-theme-quartz" style="width: 100%; height: 453px"
      :rowData="rowData"
      :columnDefs="columnDefs"
      :rowDragManaged=true
      :enableCellChangeFlash=true
      :getRowNodeId="getRowNodeId"
          @rowDragEnd="updateIndexColumn"
    >
    </AgGridVue>
  </div>
</section>
</div>
</template>

<script>
// import { createApp } from 'vue';
import "ag-grid-community/styles/ag-grid.css"; 
import "ag-grid-community/styles/ag-theme-quartz.css";
import { AgGridVue } from "ag-grid-vue3"; 
export default {
  name: 'App',
  data() {
    return {
      columnDefs: null,
      rowData: null, 
      options:["Мраморный щебень фр. 30 мм, 40кг",
      "Мраморный щебень фр. 2-5 мм, 25кг",
      "Мраморный щебень фр. 4-8 мм, 25кг",
      "Мраморный щебень фр. 3-10 мм, 25кг"],
      
    }
  },
  components: {
    AgGridVue
  },
  methods:{
  addNewCol(){
    this.rowData = [...this.rowData, {"index": "", " ":"","Наименование единицы": this.options[0],
    "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"}, ]
  },
  getRowNodeId(data) {
      return data.id;
    },
    updateIndexColumn() {
      console.log(this.rowData.ownKeys());
    //   const allRowNodes = this.gridApi.getModel().getRowNodes()
    //   allRowNodes.forEach((node, index) => {
    //     const data = node.data;
        
    //     data.index = index + 1;
    //   });
    //   this.gridApi.redrawRows();
    // },
  }},
 
  mounted() {
    
    this.columnDefs = [
    { field: 'index',
    headerName: '',
    width:40,
    valueGetter:params => params.node.rowIndex
  },
    { field: "",
      headerName: '',
      rowDrag : true,
      width:40,
    },
    { field: "Наименование единицы", editable: true,
      filter: false,
     sortable: false ,cellEditor: 'agSelectCellEditor',cellEditorParams: { values: [this.options[0],this.options[1],this.options[2],this.options[3]] },
    },
    { field: "Цена", editable: true,cellEditor: 'agTextCellEditor',},
    { field: "Кол-во", editable: true,cellEditor: 'agTextCellEditor',},
    { field: "Название товара",editable: true,cellEditor: 'agTextCellEditor', },
    { field: "Итого",},
    ],
    this.rowData = [
    {"index": "", " ":"","Наименование единицы": this.options[0],
    "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"
  },
    {"index": "" , " ": "", "Наименование единицы":this.options[0],
    "Цена" : "1500", "Кол-во":"10","Название товара":"Мраморный щебень","Итого":"1231"
  },
    {"index": "" , " ":"", "Наименование единицы": this.options[0],
    "Цена" : "1000", "Кол-во":"5","Название товара":"Мраморный щебень","Итого":"1231"
  },
    {"index": "" , " ": "", "Наименование единицы": this.options[0],
    "Цена" : "860", "Кол-во":"10","Название товара":"Мраморный щебень","Итого":"1231"
  },
    ],
this.gridApi = this.$refs.agGrid.api;
  }
  
}

  // setup()  {
  //           const rowData = ref([
  //   {"": " " , " ": " ", "Наименование единицы": "Мраморный щебень фр. 2-5 мм, 25кг",
  //   "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"
  // },
  //   {"": " " , " ": " ", "Наименование единицы": "Мраморный щебень фр. 2-5 мм, 25кг",
  //   "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"
  // },
  //   {"": " " , " ": " ", "Наименование единицы": "Мраморный щебень фр. 2-5 мм, 25кг",
  //   "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"
  // },
  //   {"": " " , " ": " ", "Наименование единицы": "Мраморный щебень фр. 2-5 мм, 25кг",
  //   "Цена" : "1231", "Кол-во":"12","Название товара":"Мраморный щебень","Итого":"1231"
  // },
    
    
  // ])

  // Column Definitions: Defines & controls grid columns.
//   const colDefs = ref([
//     { field: "staticArray",
//       headerName: '1',
//       cellClass: ['my-class1','my-class2']
//     },
//     { field: "" },
//     { field: "Наименование единицы",editable:true,rowSelection:"multiple",
//       },
//     { field: "Цена" },
//     { field: "Кол-во" },
//     { field: "Название товара" },
//     { field: "Итого" },
//   ])
// const defaultColDef = ref({
//       editable: true,
//       filter: false,
//      sortable: false 
//     });
//   return {
//     rowData,
//     colDefs,
//     defaultColDef
//   }
//   },
// }
</script>

<style lang="scss">
* {
	margin: 0;
	padding: 0;
}
@font-face {
  font-family: "MyriadPro";
  font-style: normal;
  font-display: auto;
  unicode-range: U+000-5FF;
  src: local("myriadpro"), url("../public/fonts/myriadpro/MYRIADPRO-SEMIBOLD.OTF"), format("OTF");
}
#app {
  font-family: 'MyriadPro';
  max-width: 1728px;
}
.wrap{
  display: flex;
  background-color: #fbfcfd;
  width: 1728px;
}
.wrapSection{
  margin: 25px;
  width:100%;
}
.leftAside{
  background-color: #000000;
  min-width: 229px;
  height: 100vh;
}
.title{
  margin-bottom: 25px;
  font-size: 30px;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  color: #000;
}
.links{
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1725px;
  text-align: left;
  margin-left: 25px;
  margin-bottom: 25px;
  text-transform: none;
  a{
    text-decoration: none;
    color:#1253a2 ;
    font-family: 'MyriadPro';
    font-size: 16px;
  font-weight: 600;
  }
  .secondLink{
    margin-left: 20px;
    margin-right: 25px;
    text-transform: none;
  }
}
.rightGroup{
  max-height:15px ;
  align-self: center;
  margin-right: 15px;
  button{
    background-color: rgba(255, 255, 255, 0);
    border: none;
  }
}
.newRow{
  max-width: 1449px;
  padding: 20px 0 20px 25px;
  border-radius: 10px;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  border: solid 1px var(--pale-grey);
  background-color: #fff;
  text-align: left;
}
.adderRow{
color: #ffffff;
padding: 10px 15px 10px;
border-radius: 5px;
background-color:#2f8cff ;
border: none;
font-family: 'MyriadPro';
font-size: 14px;
}
.plus{
  color: #1253a2;
  padding-right: 7px ;
height: 11px;
}
.wrapTable{
  margin-top: 25px;
  background-color: #fff;
  width: 100%;
}
.redactTable{
  display: flex;
  align-items: center;
  justify-content: flex-end;
  margin-top: 10px;
  gap: 20px;
}
.buttonsWithoutBorder{
  border: none;
  color: #a6b7d4;
  background-color: unset;
}


</style>
