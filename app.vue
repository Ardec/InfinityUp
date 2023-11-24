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
              <div @click="showBolt = !showBolt" class="btn">Pokaż szczegóły kierowców Uber</div>
              <div v-if="showBolt === true">
    <table>
      <thead>
        <tr>
          <th>Kierowca</th>
          <th>Gotówka</th>
          <th>Przelew</th>
          <th>Bonus</th>
          <th>Napiwki</th>
          <th>Utarg</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(driver, index) in driversBolt" :key="index">
          <td>{{ driver.Kierowca }}</td>
          <td>{{ driver.Gotówka }}</td>
          <td>{{ driver.Przelew }}</td>
          <td>{{ driver.Bonus }}</td>
          <td>{{ driver.Napiwki }}</td>
          <td>{{ driver.Utarg }}</td>
        </tr>
      </tbody>
    </table>
  </div>
              </div>
          </div>
          <div class="section">
            <div class="title">Uber</div>
             <input class="btn" type="file" @change="addUber" accept=".csv" />
            <div v-show="fileMatrixUber != ''" class="loaded btn">Załadowano</div>
            {{fileMatrixUber}}

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
const UberWeekBalance = ref(0);
const UberWeekCash = ref(0);
const UberPayment = ref(0);
const FreeNowWeekBalance = ref(0);
const FreeNowWeekCash = ref(0);
const FreeNowPayment = ref(0);
const showBolt = ref(false);
const showUber = ref(false);
const showFreeNow = ref(false);

let driversBolt = [
    { Kierowca: '', Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 }
];
let driversUber = [
    { Kierowca: '', Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 }
];
let driversFreeNow = [
    { Kierowca: '', Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 }
];

function addDriver(fileMatrixBolt) {

  for (let index = 1; index < fileMatrixBolt.length; index++) {
    let row = fileMatrixBolt[index];
    if (row.length > 1) {
      const driverName = capitalize(row[0]);

      // Sprawdź, czy kierowca już istnieje
      const existingDriverIndex = driversBolt.findIndex(driver => driver.Kierowca === driverName);
      if (existingDriverIndex !== -1) {
        // Jeśli kierowca już istnieje, wyświetl alert
        // alert(`Kierowca ${driverName} już istnieje w tablicy pod indeksem ${existingDriverIndex}`);
         driversBolt[existingDriverIndex].Gotówka = parseFloat((driversBolt[existingDriverIndex].Gotówka + convertToNumber(row[9]) * -1).toFixed(2));
        driversBolt[existingDriverIndex].Przelew = parseFloat((driversBolt[existingDriverIndex].Przelew + convertToNumber(row[15])).toFixed(2));
        driversBolt[existingDriverIndex].Bonus = parseFloat((driversBolt[existingDriverIndex].Bonus + convertToNumber(row[11])).toFixed(2));
        driversBolt[existingDriverIndex].Napiwki = parseFloat((driversBolt[existingDriverIndex].Napiwki + convertToNumber(row[14])).toFixed(2));
        driversBolt[existingDriverIndex].Utarg = parseFloat((driversBolt[existingDriverIndex].Utarg + convertToNumber(row[3])).toFixed(2));
      } else {
        // Jeśli kierowca nie istnieje, dodaj go do tablicy
        let newDriver = {
          Kierowca: driverName,
          Gotówka: parseFloat((convertToNumber(row[9]) * -1).toFixed(2)),
          Przelew: parseFloat(convertToNumber(row[15]).toFixed(2)),
          Bonus: parseFloat(convertToNumber(row[11]).toFixed(2)),
          Napiwki: parseFloat(convertToNumber(row[14]).toFixed(2)),
          Utarg: parseFloat(convertToNumber(row[3]).toFixed(2))
        };
        driversBolt.push(newDriver);
      }
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
    fileRowsBolt.value = fileContentBolt.value.replace(/\r/g, '').split("\n");
    fileMatrixBolt.value = fileRowsBolt.value.map((row) =>
      row.split('","').map(cell => cell.replace(/^"|"$/g, ''))
    );
    boltWeekBalance.value += convertToNumber(fileMatrixBolt.value[1][15]);
    boltWeekCash.value += convertToNumber(fileMatrixBolt.value[1][9]);
    boltPayment.value += convertToNumber(fileMatrixBolt.value[1][8]);
    addDriver(fileMatrixBolt.value);
    removeDriversWithAllZeros();
  };
  reader.readAsText(selectedFile, "UTF-8");
};

const addUber = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentUber.value = e.target.result;
    fileRowsUber.value = e.target.result.split("\n");
    fileRowsUber.value = fileContentUber.value.replace(/\r/g, '').split("\n");
    fileMatrixUber.value = fileRowsUber.value.map((row) =>
    row.split(',').map(cell => cell.replace(/^"|"$/g, ''))
    );
    // addDriverUber(fileMatrixUber.value);
    // console.log(fileMatrixUber.value[1][1])
    // UberWeekBalance.value += convertToNumber(fileMatrixUber.value[1][15]);
    // UberWeekCash.value += convertToNumber(fileMatrixUber.value[1][9]);
    // UberPayment.value += convertToNumber(fileMatrixUber.value[1][8]);
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
  };
  reader.readAsText(selectedFile, "UTF-8");

};

function convertToNumber(string) {
  return parseFloat(string.replace(',', '.'));
}

function capitalize(str) {
  return str.toLowerCase().split(' ').map(word => word.charAt(0).toUpperCase() + word.slice(1)).join(' ');
}

function removeDriversWithAllZeros() {
  driversBolt = driversBolt.filter(driver => {
    // Sprawdza, czy jakakolwiek wartość nie jest równa zero
    return driver.Gotówka !== 0 ||
           driver.Przelew !== 0 ||
           driver.Bonus !== 0 ||
           driver.Napiwki !== 0 ||
           driver.Utarg !== 0;
  });
}

onMounted(() => {
  driversBolt.value = [
    { Kierowca: '', Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 }
  ];
});

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
