<template>
  <div class="container">
    <div class="title">InfinityUp Management</div>
    <div class="input-container">
      <div class="input-content">
        <div class="input-title-content">Wprowadź dane</div>
        <div class="input-subtitle-content">Dodaj wcześniej przygotowane pliki wsadowe CSV </div>
        <div class="input-section">
          <div class="section">
            <div class="title">Bolt</div>
            <input class="btn" type="file" @change="addBolt" accept=".csv" />
            <div v-show="fileMatrixBolt != ''" class="loaded btn">Załadowano</div>
            <div class="summary" v-show="fileMatrixBolt != ''">
              <br>
                      <div class="input-title-content">Sprzedaż w okresie - {{ (boltWeekBalance + boltWeekCash*-1).toFixed(2)}}</div>
                      <br>
              <div>Przelew: {{boltWeekBalance}}</div>
              <div>Gotówka: {{boltWeekCash*-1}}</div>
              <br>
              <div class="input-title-content">Opłata - {{(boltPayment*-1).toFixed(2)}}</div>
              <div class="input-title-content">Bilans - {{((boltWeekBalance + boltWeekCash*-1).toFixed(2) - (boltPayment*-1).toFixed(2)).toFixed(2)}}</div>
              <div class="btn">Pokaż szczegóły kierowców Uber</div>
              </div>
          </div>
          <div class="section">
            <div class="title">Uber</div>
             <input class="btn" type="file" @change="addUber" accept=".csv" />
            <div v-show="fileMatrixUber != ''" class="loaded btn">Załadowano</div>

          </div>
          <div class="section">
            <div class="title">FreeNow</div>
            <input class="btn" type="file" @change="addFreeNow" accept=".csv" />
            <div v-show="fileMatrixFreeNow != ''" class="loaded btn">Załadowano</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const boltWeekBalance = ref(0);
const boltWeekCash = ref(0);
const boltPayment = ref(0);

let driversBolt = [
    { Kierowca: '', Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 }
];

function addDriver(fileMatrixBolt) {
  console.log(fileMatrixBolt)
  for (let row of fileMatrixBolt) { // Teraz iterujemy po właściwej wartości, nie po 'ref'
    if (row.length > 1) {
      let newDriver = {
        Kierowca: row[0], 
        Gotówka: row[1], 
        Przelew: row[2],
        Bonus: row[3],
        Napiwki: row[4],
        Utarg: row[5]
      };
      driversBolt.push(newDriver);
    } else {
      break;
    }
  }
}

const fileMatrixBolt = ref([]);
const fileContentBolt = ref("");
const fileRowsBolt = ref([]);

const fileMatrixUber = ref([]);
const fileContentUber = ref("");
const fileRowsUber = ref([]);

const fileMatrixFreeNow = ref([]);
const fileContentFreeNow = ref("");
const fileRowsFreeNow = ref([]);

const addBolt = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentBolt.value = e.target.result;
    fileRowsBolt.value = e.target.result.split("\n");
    // fileMatrixBolt.value = fileRowsBolt.value.map((row) => row.split('","'));
    fileRowsBolt.value = fileContentBolt.value.replace(/\r/g, '').split("\n");
    fileMatrixBolt.value = fileRowsBolt.value.map((row) =>
      row.split('","').map(cell => cell.replace(/^"|"$/g, ''))
    );
    boltWeekBalance.value += convertToNumber(fileMatrixBolt.value[1][15]);
    boltWeekCash.value += convertToNumber(fileMatrixBolt.value[1][9]);
    boltPayment.value += convertToNumber(fileMatrixBolt.value[1][8]);
    console.log("FILEMATRIX_BOLT",fileMatrixBolt.value);
    addDriver(fileMatrixBolt.value);
  };
  reader.readAsText(selectedFile, "UTF-8");
  console.log("DRIVERS-BOLT",driversBolt);
};

function convertToNumber(string) {
  return parseFloat(string.replace(',', '.'));
}

const addUber = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentUber.value = e.target.result;
    fileRowsUber.value = e.target.result.split("\n");
    fileMatrixUber.value = fileRowsUber.value.map((row) => row.split(";"));
  };
  reader.readAsText(selectedFile, "UTF-8");

};

const addFreeNow = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentFreeNow.value = e.target.result;
    fileRowsFreeNow.value = e.target.result.split("\n");
    fileMatrixFreeNow.value = fileRowsFreeNow.value.map((row) => row.split(";"));
    console.log(fileMatrixFreeNow.value);
  };
  reader.readAsText(selectedFile, "UTF-8");

};

</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@100;400&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");

* {
  font-family: "Inter", sans-serif;
}
.container {
  margin: 16px 24px;
}
.title {
  display: flex;
  text-align: center;
  font-size: 22px;
  line-height: 160%;
  font-weight: 400;
}
.input-container{
    margin-top: 100px;
}
.input-content {
    
}
.input-title-content {
  display: flex;
  text-align: center;
  font-size: 22px;
  line-height: 160%;
  font-weight: 400;
}
.input-subtitle-content {
  display: flex;
  text-align: center;
  font-size: 16px;
  line-height: 160%;
}
.input-section {
  margin-top:16px;
  display: flex;
  justify-content: space-around;
}
.btn{
  margin-top:16px;
  border:solid black 1px;
  border-radius:4px;
  padding:8px 12px;
}
.btn:hover{
  cursor:pointer;
  opacity:0.6;
}
.section{
  display:flex;
  padding:32px;
  flex-direction: column;
}
.loaded{
  background-color: green;
  color:white;
}
</style>
