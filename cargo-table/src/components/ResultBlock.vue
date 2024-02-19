<template>
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
</template>
<script>
export default{
name:'ResultBlock',
props:['rowData'],
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
}
</script>