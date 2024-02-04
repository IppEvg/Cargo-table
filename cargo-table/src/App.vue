<template>
<div class="wrap">
<aside class="leftAside"></aside>
<section class="wrapSection">
  <h1 class="title">Проведение ТО и мелкий ремонт</h1>
  <div class="links">
    <div class="leftGroup">
      <a href="">Общее</a>
      <a class="secondLink" href="">Товары</a>
      <a href="">Доп. расходы</a>
    </div>
    <div class="rightGroup"><button><img src="./assets/svg/combined-shape.svg" alt="settingsIcon"></button></div>
  </div>
  <div class="newRow">
    <button class="adderRow"><span class="plus">+</span> Добавить строку</button>
  </div>
</section>
</div>
</template>

<script>


export default {
  name: 'App',
  components: {

  }
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
.wrap{
  display: flex;
  background-color: #fbfcfd;
}
.wrapSection{
  margin: 25px;
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
  }
}
.rightGroup{
  max-height:15px ;
  align-self: center;
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
</style>
