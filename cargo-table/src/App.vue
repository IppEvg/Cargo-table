<template> 
  <div class="mainWrapper">
    <div class="wrap"> 
      <aside class="leftAside"></aside> 
      <section class="wrapSection"> 
        <div class="burger">
          <button>
            <img src="./assets/svg/burger.svg" alt="burgerImg">
          </button>
          <h1 class="title">Проведение ТО и мелкий ремонт</h1>
        </div>
        <div class="links"> 
          <div class="leftGroup"> 
            <a href="#">Общее</a> 
            <a class="secondLink" href="#">Товары</a> 
            <a href="#">Доп. расходы</a> 
          </div> 
          <div class="rightGroup"> 
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
              <button class="buttonsWithoutBorder" @click="sendDataToServer" >Сохранить изменения</button> 
            </div> 
            <div class="rightGroup"> 
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
                        <input type="checkbox" id="idx" 
                        @change="onRowDataUpdated(column.field)" 
                        checked="true" /> 
                        &nbsp; 
                        <label for=" idx">{{ column.field }}</label> 
                      </div> 
                    </div> 
                  </div> 
                </button> 
              </div> 
            </div> 
          </div> 
          <AgGridVue  class="ag-theme-quartz" style="min-width: max-content;" 
            :rowData="rowData" 
            :columnDefs="listMenu[0].listColumns" 
            :rowDragManaged=true 
            :onGridReady="onGridReady" 
            :enableCellChangeFlash=true 
            @visibleChanged="onRowDataUpdated" 
            @RowDragEnd="onRowDragEnd" > 
          </AgGridVue> 
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
    this.rowData.push({id:this.rowData.length+1,index: "", " ": "", "Наименование единицы": this.options[0], 
      "Цена": 1231, "Кол-во": 12, "Название товара": "Мраморный щебень", "Итого": ""})
      this.rowData=[...this.rowData]
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
  onRowDragEnd(){
    const columnDef = this.listMenu[0].listColumns.map((e,i)=>e.index=i)
    this.gridApi.setColumnDefs([...this.listMenu[0].listColumns,columnDef]); 
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
    }
}, 
computed: { 
  summCosts(){
    if(this.rowData){
      return this.rowData.reduce((sum,el)=>sum+el["Кол-во"]*el["Цена"],0)
    }
  return 0
  },
  summPoints(){
    if(this.rowData){
      return this.rowData.reduce((sum,el)=>sum+el["Кол-во"],0)
    }
  return 0
  },
  summWeights(){
    if(this.rowData){
      return this.rowData.reduce((sum,el)=>
      sum+(+el["Наименование единицы"].match(/\d+кг/i)[0].match(/\d+/i))*
      el["Кол-во"],0)
    }
  return 0
  },
}, 
mounted() { 
  this.listMenu[0].listColumns = [ 
    { 
      field: 'Индекс', 
      hide: false, 
      headerName: '', 
      width: 50, 
      editable: false, 
      valueGetter: params => params.node.rowIndex ,
    }, 
    { 
      field: "Перемещение", 
      hide: false, 
      headerName: '', 
      rowDrag: true, 
      width:50
      
    }, 
    { 
      field: "Наименование единицы",
      editable: true, 
      filter: false, 
      sortable: false, 
      cellEditor: 'agSelectCellEditor', 
      flex:2.1, 
      hide: false,
      singleClickEdit:true,
      onCellValueChanged:(params)=>{
        switch (params.data["Наименование единицы"]) {
          case this.options[0]:
          this.rowData[params.node.rowIndex]["Цена"]=1231
          break
          case this.options[1]:
          this.rowData[params.node.rowIndex]["Цена"]=1500
          break
          case this.options[2]:
          this.rowData[params.node.rowIndex]["Цена"]=1000
          break
          case this.options[3]:
          this.rowData[params.node.rowIndex]["Цена"]=860
          break
        }
        return this.rowData=[...this.rowData]
      },
      valueGetter:params=>params.data["Наименование единицы"],
      cellEditorParams: { values: 
        [this.options[0], this.options[1], this.options[2], this.options[3]]
       }, 
    }, 
    { field: "Цена", editable: true, cellEditor: 'agTextCellEditor',
    singleClickEdit:true, hide: false, flex:1 }, 
    { field: "Кол-во", editable: true, cellEditor: 'agTextCellEditor', 
    singleClickEdit:true, hide: false,flex:1 }, 
    { field: "Название товара", 
      editable: true, 
      cellEditor: 'agTextCellEditor', 
      flex:1.6, 
      hide: false, 
    }, 
    {field: "Итого", 
       flex:1, 
      hide: false, 
      valueGetter: (params) => params.data["Итого"] = params.data["Цена"] * params.data["Кол-во"] 
    }, 
  ] 
  this.rowData = [ 
    { 
      id: 1, index: "", " ": "", "Наименование единицы": this.options[0], 
      "Цена": 1231, "Кол-во": 12, "Название товара": "Мраморный щебень", "Итого": "" 
    }, 
    { 
      id: 2, index: "", " ": "", "Наименование единицы": this.options[1], 
      "Цена": 1500, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "" 
    }, 
    { 
      id: 3, index: "", " ": "", "Наименование единицы": this.options[2], 
      "Цена": 1000, "Кол-во": 5, "Название товара": "Мраморный щебень", "Итого": "" 
    }, 
    { 
      id: 4, index: "", " ": "", "Наименование единицы": this.options[3], 
      "Цена": 860, "Кол-во": 10, "Название товара": "Мраморный щебень", "Итого": "" 
    }, 
  ] 
}, 
} 
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
.mainWrapper{
  display: flex;
  flex-direction: column;
  height: 100%;
  min-height: 100vh;
}
.wrap { 
display: flex; 
background-color: #fbfcfd; 
max-width: 1728px; 
flex-grow: 1;
height: fit-content;
} 

.wrapSection { 
margin: 25px; 
width: 100%; 
} 

.leftAside { 
background-color: #000000; 
min-width: 229px; 
flex-grow: 1;
} 
.burger{
  button{
    display: none;
    img{
      padding: 12px 0 0 10px;
    }
    
  }
  display: flex;
  align-items: flex-start;
  margin-bottom: 25px;
  gap: 25px;

}
.title { 
font-size: 30px; 
font-weight: normal; 
font-stretch: normal; 
font-style: normal; 
line-height: normal; 
letter-spacing: normal; 
color: #000; 
} 

.links { 
display: flex; 
justify-content: space-between; 
align-items: center; 
max-width: 1725px; 
text-align: left; 
margin-left: 25px; 
margin-bottom: 25px; 
text-transform: none; 

a { 
  text-decoration: none; 
  color: #1253a2; 
  font-family: 'MyriadPro'; 
  font-size: 16px; 
  font-weight: 600; 
} 

.secondLink { 
  margin-left: 20px; 
  margin-right: 25px; 
  text-transform: none; 
} 
} 

.rightGroup { 
position: relative; 
max-height: 15px; 
align-self: center; 
margin-right: 15px; 

button { 
  display: flex; 
  justify-content: space-between; 
  gap: 5px; 
  background-color: rgba(255, 255, 255, 0); 
  border: none; 
  height: 15px; 
} 
} 

.listOptions { 
position: absolute; 
top: 20px; 
right: 0px; 
width: max-content; 
display: flex; 
flex-direction: column; 
align-items: flex-end; 
border: 1px solid black; 
border-radius: 5px; 
background-color: #fff; 
z-index: 1;

.wrapButtonColumnMenu { 
  position: relative; 

  .listColumn { 
    display: flex; 
    flex-direction: column; 
    align-items: flex-start; 
    position: absolute; 
    top: 30px; 
    right: 0; 
    min-width: max-content; 
    background-color: #fff; 
    border: 1px solid black; 
    z-index: 2; 
    padding: 7px; 
    gap: 5px; 
    border-radius: 5px; 

    label { 
      pointer-events: none; 
    } 
  } 
} 

.buttonColumnMenu { 
  position: relative; 
  width: 100%; 
  height: fit-content; 
  padding: 7px; 
  font-size: 14px; 
  display: flex; 
  align-items: center; 
  // .{} 
} 
} 

.buttonColumnMenu:hover { 
background-color: #eef3f8; 
} 

.newRow { 
max-width: 1449px; 
padding: 20px 0 20px 25px; 
border-radius: 10px; 
box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07); 
border: solid 1px var(--pale-grey); 
background-color: #fff; 
text-align: left; 
} 

.adderRow { 
color: #ffffff; 
padding: 10px 15px 10px; 
border-radius: 5px; 
background-color: #2f8cff; 
border: none; 
font-family: 'MyriadPro'; 
font-size: 14px; 
} 

.plus { 
color: #1253a2; 
padding-right: 7px; 
height: 11px; 
} 

.wrapTable { 
margin-top: 25px; 
background-color: #fff; 
width: 100%; 
border-radius: 10px;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  border: solid 1px #fff;
} 

.redactTable { 
display: flex; 
align-items: center; 
justify-content: flex-end; 
margin: 10px 0 15px; 
gap: 20px; 
} 

.buttonsWithoutBorder { 
border: none; 
color: #a6b7d4; 
background-color: unset; 
} 
.wrapResult{
  display: flex;
  justify-content: flex-end;
  width: 100%;
  margin-top: 15px;
  margin-bottom: 25px;
  
}
.wrapResult_right{
  min-width: 304px;
min-height: 10px;
display: flex;
flex-direction: column;
  font-size: 14px;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  color: #8f8f8f;
  gap:5px;
  margin-right: 15px;
    .preResult{
  min-width:304px;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  border: solid 1px #eeeff1;
  background-color: #fbfcfd;
   padding: 15px;
   gap:15px;
}
}

.rowForSumm{
  display: flex;
  justify-content: space-between;
 
}
.summ{
  color: #000;
}

.ag-root-wrapper-body.ag-layout-normal {
  height: 100%;
}
.ag-cell-value{
text-overflow: clip;
}
@media screen and (max-width: 800px){
  .leftAside{
    display: none;
  }
}
  @media screen and (max-width: 650px){

    .wrap{
      margin: 16px 10px 0;
    }
    .wrapSection{
      margin: 0;
    }
    .burger{
      button{
        display: contents;
      }
      .title{
      font-size: 30px;
    }
    }
    
    .links{
      margin-left: 0;
    }
    .rightGroup,.redactTable{
      display: none;
    }
    


    .wrapResult{
      margin-top: 25px;
      .preResult{
        font-size: 16px;
      }
      
    }
  }
</style> 
