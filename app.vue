<template>
  <div class="container">
    <div class="title">InfinityUp Management</div>
    <div class="input-container">
      <div class="input-content">
        <hr />
        <div class="input-title-content">Wprowadź dane floty</div>
        <div class="input-subtitle-content">
          Dodaj wcześniej przygotowane pliki wsadowe CSV
        </div>
        <hr />

        <!-- Sekcja jeden  -->

        <!-- Dodawanie mojego przygotowanego pliku  -->
        <div class="input-section">
          <div class="section">
            <div class="title">Przypisanie kart do kierowców</div>
            <input class="btn" type="file" @change="addInfinity" accept=".csv" />
            <div v-show="fileMatrixInfinity != ''" class="loaded btn">Załadowano</div>
            <div class="summary" v-show="fileMatrixInfinity != ''">
              <div @click="showInfinity = !showInfinity" class="btn">
                Pokaż szczegóły kierowców
              </div>
              <div v-if="showInfinity === true">
                   <table>
                  <thead>
                    <tr>
                      <th>Numer Orlen</th>
                      <th>Numer Infinity</th>
                      <th>Imię i Nazwisko</th>
                      <th>Sposoób rozliczenia</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="row in fileMatrixInfinity" :key="row">
                      <td>{{ row[0] }}</td>
                      <td>{{ row[1] }}</td>
                      <td>{{ row[2] }}</td>
                      <td>{{ row[3] }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>

          <!-- Dodawanie zestawienia Orlenu  -->

          <div class="section">
            <div class="title">Zestawienie Orlen</div>
            <input class="btn" type="file" @change="addOrlen" accept=".csv" />
            <div v-show="fileMatrixOrlen != ''" class="loaded btn">Załadowano</div>
            <div class="summary" v-show="fileMatrixOrlen != ''">
              <div @click="showOrlen = !showOrlen" class="btn">Pokaż wczytany plik</div>
              <div v-if="showOrlen === true">
                <table>
                  <thead>
                    <tr>
                      <th>Numer karty</th>
                      <th>Ilość transakcji</th>
                      <th>Ilość pobrana</th>
                      <th>Wartość transakcji</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="row in fileMatrixOrlen" :key="row">
                      <td>{{ row[0] }}</td>
                      <td>{{ row[1] }}</td>
                      <td>{{ row[2] }}</td>
                      <td>{{ row[3] }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>

        <!-- Sekcja 2  -->

        <hr />
        <div class="input-title-content">Wprowadź dane z kierowców</div>
        <div class="input-subtitle-content">
          Dodaj wcześniej przygotowane pliki wsadowe CSV
        </div>
        <hr />
        <div class="input-section">
          <div class="section">
            <div class="title">Bolt</div>
            <input class="btn" type="file" @change="addBolt" accept=".csv" />
            <div v-show="fileMatrixBolt != ''" class="loaded btn">Załadowano</div>
            <div class="summary" v-show="fileMatrixBolt != ''">
              <br />
              <div class="input-title-content">
                Sprzedaż w okresie -
                {{ (boltWeekBalance + boltWeekCash * -1).toFixed(2) }}
              </div>
              <br />
              <div>Przelew: {{ boltWeekBalance }}</div>
              <div>Gotówka: {{ boltWeekCash * -1 }}</div>
              <br />
              <div class="input-title-content">
                Opłata - {{ (boltPayment * -1).toFixed(2) }}
              </div>
              <div class="input-title-content">
                Bilans -
                {{
                  (
                    (boltWeekBalance + boltWeekCash * -1).toFixed(2) -
                    (boltPayment * -1).toFixed(2)
                  ).toFixed(2)
                }}
              </div>
              <div @click="showBolt = !showBolt" class="btn">
                Pokaż szczegóły kierowców Uber
              </div>
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
            <div class="summary" v-show="fileMatrixUber != ''">
              <br />
              <div class="input-title-content">
                Sprzedaż w okresie - {{ uberWeekBalance }}
              </div>
              <br />
              <div>Przelew: {{ uberWeekBank }}</div>
              <div>Gotówka: {{ uberWeekCash }}</div>
              <br />
              <div @click="showUber = !showUber" class="btn">
                Pokaż szczegóły kierowców Uber
              </div>
            </div>
            <div v-if="showUber === true">
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
                  <tr v-for="(driver, index) in driversUber" :key="index">
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
          <div class="section">
            <div class="title">FreeNow</div>
            <input class="btn" type="file" @change="addFreeNow" accept=".csv" />
            <div v-show="fileMatrixFreeNow != ''" class="loaded btn">Załadowano</div>
            <div class="summary" v-show="fileMatrixFreeNow != ''">
              <br />
              <div class="input-title-content">
                Sprzedaż w okresie - {{ freeNowWeekBalance }}
              </div>
              <br />
              <div>Przelew: {{ freeNowWeekBank }}</div>
              <div>Gotówka: {{ freeNowWeekCash }}</div>
              <br />
              <div class="input-title-content">
                Opłata - {{ freeNowPayment.toFixed(2) }}
              </div>
              <div class="input-title-content">
                Bilans -
                {{
                  (freeNowWeekBalance.toFixed(2) - freeNowPayment.toFixed(2)).toFixed(2)
                }}
              </div>
              <div @click="showFreeNow = !showFreeNow" class="btn">
                Pokaż szczegóły kierowców Uber
              </div>
            </div>
            <div v-if="showFreeNow === true">
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
                  <tr v-for="(driver, index) in driversFreeNow" :key="index">
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
      </div>
    </div>
    <hr />
    <div class="input-title-content">Podsumowanie</div>
    <hr />
    <br />
    <div class="input-title-content">
      Utarg w okresie
      {{
        (
          boltWeekBalance +
          uberWeekBank +
          freeNowWeekBank +
          boltWeekCash * -1 +
          uberWeekCash +
          freeNowWeekCash
        ).toFixed(2)
      }}
    </div>
    <br />
    <div class="input-subtitle-content">
      W tym przelewem {{ (boltWeekBalance + uberWeekBank + freeNowWeekBank).toFixed(2) }}
    </div>
    <div class="input-subtitle-content">
      W tym gotówką {{ (boltWeekCash * -1 + uberWeekCash + freeNowWeekCash).toFixed(2) }}
    </div>
    <br />
    <hr />
    <div class="input-title-content">Statystyki kierowców ze wszystkich danych</div>
    <div @click="calculateDrivers()" class="btn">Oblicz</div>
    <table v-if="allDrivers.length > 1">
      <thead>
        <tr>
          <th>Kierowca</th>
          <th>Gotówka</th>
          <th>Przelew</th>
          <th>Bonus</th>
          <th>Napiwki</th>
          <th>Utarg</th>
          <th>Paliwo</th>
          <th>Pensja wg wzoru</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(driver, index) in allDrivers" :key="index">
          <td>{{ driver.Kierowca }}</td>
          <td>{{ driver.Gotówka.toFixed(2) }}</td>
          <td>{{ driver.Przelew.toFixed(2) }}</td>
          <td>{{ driver.Bonus.toFixed(2) }}</td>
          <td>{{ driver.Napiwki.toFixed(2) }}</td>
          <td>{{ driver.Utarg.toFixed(2) }}</td>
          <td>{{ driver.Paliwo }}</td>
          <td>{{ driver.Pensja }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref } from "vue";

const boltWeekBalance = ref(0);
const boltWeekCash = ref(0);
const boltPayment = ref(0);

const uberWeekBalance = ref(0);
const uberWeekCash = ref(0);
const uberWeekBank = ref(0);
const uberPayment = ref(0);

const freeNowWeekBalance = ref(0);
const freeNowWeekCash = ref(0);
const freeNowWeekBank = ref(0);
const freeNowPayment = ref(0);

const showBolt = ref(false);
const showUber = ref(false);
const showFreeNow = ref(false);
const showOrlen = ref(false);
const showInfinity = ref(false);

let driversBolt = [
  { Kierowca: "", Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 },
];
let driversUber = [
  { Kierowca: "", Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 },
];
let driversFreeNow = [
  { Kierowca: "", Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 },
];
const allDrivers = ref([
  { Kierowca: "", Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0, Paliwo: 0, Pensja: 0, },
]);

const fileMatrixBolt = ref([]);
const fileContentBolt = ref("");
const fileRowsBolt = ref([]);

const fileMatrixUber = ref([]);
const fileContentUber = ref("");
const fileRowsUber = ref([]);

const fileMatrixFreeNow = ref([]);
const fileContentFreeNow = ref("");
const fileRowsFreeNow = ref([]);

const fileMatrixOrlen = ref([]);
const fileContentOrlen = ref("");
const fileRowsOrlen = ref([]);

const fileMatrixInfinity = ref([]);
const fileContentInfinity = ref("");
const fileRowsInfinity = ref([]);

// Wczytywanie pliku Infinity

const addInfinity = (event) => {
  console.log("etap 1")
  const selectedFile = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    fileContentInfinity.value = e.target.result;
    fileRowsInfinity.value = e.target.result.split("\n");
    fileRowsInfinity.value = fileContentInfinity.value.replace(/\r/g, "").split("\n");
    fileMatrixInfinity.value = fileRowsInfinity.value.map((row) =>
      row.split(",").map((cell) => cell.replace(/^"|"$| z�$/g, ""))
    );
    fileMatrixInfinity.value.shift();
    console.log(fileMatrixInfinity.value)

  };
  reader.readAsText(selectedFile, "UTF-8");
};

// Wczytywanie pliku Orlen

const addOrlen = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    fileContentOrlen.value = e.target.result;
    fileRowsOrlen.value = e.target.result.split("\n");
    fileRowsOrlen.value = fileContentOrlen.value.replace(/\r/g, "").split("\n");
    fileMatrixOrlen.value = fileRowsOrlen.value.map((row) =>
      row.split(";").map((cell) => cell.replace(/^"|"$| z�$/g, ""))
    );
    fileMatrixOrlen.value.shift();
  };
  reader.readAsText(selectedFile, "UTF-8");
};

// Wczytywanie pliku Bolt

const addBolt = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    fileContentBolt.value = e.target.result;
    fileRowsBolt.value = e.target.result.split("\n");
    fileRowsBolt.value = fileContentBolt.value.replace(/\r/g, "").split("\n");
    fileMatrixBolt.value = fileRowsBolt.value.map((row) =>
      row.split('","').map((cell) => cell.replace(/^"|"$/g, ""))
    );
    boltWeekBalance.value += convertToNumber(fileMatrixBolt.value[1][15]);
    boltWeekCash.value += convertToNumber(fileMatrixBolt.value[1][9]);
    boltPayment.value += convertToNumber(fileMatrixBolt.value[1][8]);
    addDriver(fileMatrixBolt.value);
    removeDriversWithAllZeros();
  };
  reader.readAsText(selectedFile, "UTF-8");
};

// Tworzenie kierowców Bolt

function addDriver(fileMatrixBolt) {
  for (let index = 2; index < fileMatrixBolt.length; index++) {
    let row = fileMatrixBolt[index];
    if (row.length > 1) {
      const driverName = capitalize(row[0]);

      // Sprawdź, czy kierowca już istnieje
      const existingDriverIndex = driversBolt.findIndex(
        (driver) => driver.Kierowca === driverName
      );
      if (existingDriverIndex !== -1) {
        // Jeśli kierowca już istnieje, wyświetl alert
        // alert(`Kierowca ${driverName} już istnieje w tablicy pod indeksem ${existingDriverIndex}`);
        driversBolt[existingDriverIndex].Gotówka = parseFloat(
          (
            driversBolt[existingDriverIndex].Gotówka +
            convertToNumber(row[9]) * -1
          ).toFixed(2)
        );
        driversBolt[existingDriverIndex].Przelew = parseFloat(
          (driversBolt[existingDriverIndex].Przelew + convertToNumber(row[15])).toFixed(2)
        );
        driversBolt[existingDriverIndex].Bonus = parseFloat(
          (driversBolt[existingDriverIndex].Bonus + convertToNumber(row[11])).toFixed(2)
        );
        driversBolt[existingDriverIndex].Napiwki = parseFloat(
          (driversBolt[existingDriverIndex].Napiwki + convertToNumber(row[14])).toFixed(2)
        );
        driversBolt[existingDriverIndex].Utarg = parseFloat(
          (driversBolt[existingDriverIndex].Utarg + convertToNumber(row[3])).toFixed(2)
        );
      } else {
        // Jeśli kierowca nie istnieje, dodaj go do tablicy
        let newDriver = {
          Kierowca: driverName,
          Gotówka: parseFloat((convertToNumber(row[9]) * -1).toFixed(2)),
          Przelew: parseFloat(convertToNumber(row[15]).toFixed(2)),
          Bonus: parseFloat(convertToNumber(row[11]).toFixed(2)),
          Napiwki: parseFloat(convertToNumber(row[14]).toFixed(2)),
          Utarg: parseFloat(convertToNumber(row[3]).toFixed(2)),
        };
        driversBolt.push(newDriver);
      }
    } else {
      break;
    }
  }
}

// Wczytywanie pliku Uber

const addUber = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentUber.value = e.target.result;
    fileRowsUber.value = e.target.result.split("\n");
    fileRowsUber.value = fileContentUber.value.replace(/\r/g, "").split("\n");
    fileMatrixUber.value = fileRowsUber.value.map((row) =>
      row.split(",").map((cell) => cell.replace(/^"|"$/g, ""))
    );
    addDriverUber(fileMatrixUber.value);
    uberWeekBalance.value = parseFloat(
      driversUber.reduce((sum, driver) => sum + driver.Utarg, 0).toFixed(2)
    );
    uberWeekBank.value = parseFloat(
      driversUber.reduce((sum, driver) => sum + driver.Przelew, 0).toFixed(2)
    );
    uberWeekCash.value = parseFloat(
      driversUber.reduce((sum, driver) => sum + driver.Gotówka, 0).toFixed(2)
    );
  };
  reader.readAsText(selectedFile, "UTF-8");
};

// Tworzenie kierowców Uber

function addDriverUber(fileMatrixUber) {
  driversUber = [];
  for (let index = 1; index < fileMatrixUber.length; index++) {
    let row = fileMatrixUber[index];
    if (row.length > 1) {
      let newDriver = {
        Kierowca: capitalize(row[1]) + " " + capitalize(row[2]),
        Gotówka: parseFloat((convertToNumberWithDefault(row[5], 0) * -1).toFixed(2)),
        Przelew: parseFloat(convertToNumberWithDefault(row[3], 0).toFixed(2)),
        Bonus: parseFloat(convertToNumberWithDefault(row[17], 0).toFixed(2)),
        Napiwki: parseFloat(convertToNumberWithDefault(row[16], 0).toFixed(2)),
        Utarg: parseFloat(convertToNumberWithDefault(row[4], 0).toFixed(2)),
      };
      if (newDriver.Kierowca == "Kamil Muskus" && newDriver.Przelew < 0) {
        console.log("#");
        newDriver.Przelew = newDriver.Przelew * -1;
      }
      driversUber.push(newDriver);
    }
  }
}

const addFreeNow = (event) => {
  const selectedFile = event.target.files[0];
  const reader = new FileReader();

  reader.onload = (e) => {
    fileContentFreeNow.value = e.target.result;
    fileRowsFreeNow.value = e.target.result.split("\n");
    fileRowsFreeNow.value = fileContentFreeNow.value.replace(/\r/g, "").split("\n");
    fileMatrixFreeNow.value = fileRowsFreeNow.value.map((row) =>
      row.split(",").map((cell) => cell.replace(/^"|"$/g, ""))
    );
    addDriverFreeNow(fileMatrixFreeNow.value);
    removeDriversWithAllZerosFreeNow();
    freeNowWeekBalance.value = parseFloat(
      driversFreeNow.reduce((sum, driver) => sum + driver.Utarg, 0).toFixed(2)
    );
    freeNowWeekBank.value = parseFloat(
      driversFreeNow.reduce((sum, driver) => sum + driver.Przelew, 0).toFixed(2)
    );
    freeNowWeekCash.value = parseFloat(
      driversFreeNow.reduce((sum, driver) => sum + driver.Gotówka, 0).toFixed(2)
    );
  };

  reader.readAsText(selectedFile, "UTF-8");
}; // <-- Dodany zamykający nawias klamrowy

function addDriverFreeNow(fileMatrixFreeNow) {
  driversFreeNow = [];
  for (let index = 1; index < fileMatrixFreeNow.length; index++) {
    let row = fileMatrixFreeNow[index];
    if (row.length > 1) {
      let newDriver = {
        Kierowca: capitalize(row[1]),
        Gotówka: parseFloat(convertToNumberWithDefault(row[5], 0).toFixed(2)),
        Przelew: parseFloat(convertToNumberWithDefault(row[20] - row[5], 0).toFixed(2)),
        Bonus: parseFloat(convertToNumberWithDefault(row[7], 0).toFixed(2)),
        Napiwki: parseFloat(convertToNumberWithDefault(row[8], 0).toFixed(2)),
        Utarg: parseFloat(convertToNumberWithDefault(row[20], 0).toFixed(2)),
      };
      (freeNowPayment.value += parseFloat(
        convertToNumberWithDefault(row[17], 0).toFixed(2)
      )),
        driversFreeNow.push(newDriver);
    }
  }
}

function convertToNumber(string) {
  return parseFloat(string.replace(",", "."));
}

function convertToNumberWithDefault(value, defaultValue = 0) {
  const number = parseFloat(value);
  return isNaN(number) ? defaultValue : number;
}

function capitalize(str) {
  return str
    .toLowerCase()
    .split(" ")
    .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
    .join(" ");
}

function removeDriversWithAllZeros() {
  driversBolt = driversBolt.filter((driver) => {
    // Sprawdza, czy jakakolwiek wartość nie jest równa zero
    return (
      driver.Gotówka !== 0 ||
      driver.Przelew !== 0 ||
      driver.Bonus !== 0 ||
      driver.Napiwki !== 0 ||
      driver.Utarg !== 0
    );
  });
}

function removeDriversWithAllZerosFreeNow() {
  driversFreeNow = driversFreeNow.filter((driver) => {
    // Sprawdza, czy jakakolwiek wartość nie jest równa zero
    return (
      driver.Gotówka !== 0 ||
      driver.Przelew !== 0 ||
      driver.Bonus !== 0 ||
      driver.Napiwki !== 0 ||
      driver.Utarg !== 0
    );
  });
}

function driversSummary(driversBolt, driversUber, driversFreeNow) {
  let combinedDrivers = [];

  [driversBolt, driversUber, driversFreeNow].forEach((driverList) => {
    if (driverList) {
      driverList.forEach((driver) => {
        addOrUpdateDriver(combinedDrivers, driver);
      });
    }
  });

  function addOrUpdateDriver(driverArray, driver) {
    const existingDriverIndex = driverArray.findIndex(
      (d) => d.Kierowca === driver.Kierowca
    );
    if (existingDriverIndex !== -1) {
      // Aktualizacja istniejącego kierowcy
      driverArray[existingDriverIndex].Gotówka += driver.Gotówka;
      driverArray[existingDriverIndex].Przelew += driver.Przelew;
      driverArray[existingDriverIndex].Bonus += driver.Bonus;
      driverArray[existingDriverIndex].Napiwki += driver.Napiwki;
      driverArray[existingDriverIndex].Utarg += driver.Utarg;
    } else {
      // Dodanie nowego kierowcy
      driverArray.push({ ...driver });
    }
  }
  return combinedDrivers;
}

function calculateDrivers() {
  allDrivers.value = driversSummary(driversBolt, driversUber, driversFreeNow);
  updateFuelData(allDrivers.value, fileMatrixInfinity.value, fileMatrixOrlen.value);
  // updateEarnings(allDrivers.value, fileMatrixInfinity.value)
}

// function updateEarnings(drivers, fileMatrixInfinity) {

//   drivers.forEach(driver => {
//     fileMatrixInfinity.forEach(infinity =>{
//       if(infinity[2])
//     })

//   })
// }

function updateFuelData(drivers, fileMatrixInfinity, fileMatrixOrlen) {

  drivers.forEach(driver => {

    fileMatrixInfinity.forEach(infinity => {
       if (capitalize(infinity[2]) == driver.Kierowca) {
        let paliwo = infinity[0]
        let pensja = infinity[3]
        driver.Paliwo = parseFloat(infinity[0]) || 0;
          fileMatrixOrlen.forEach(orlen =>{
            if(paliwo == orlen[0]){
              driver.Paliwo = parseFloat(orlen[3]) || 0;
            }
          })
          if(pensja == "A"){driver.Pensja = (((driver.Utarg - driver.Napiwki)*0.5)-(driver.Paliwo)).toFixed(2)}
          if(pensja == "B"){driver.Pensja = ((driver.Utarg - driver.Napiwki)*0.4).toFixed(2)}
    }
    })
    
  });
}


onMounted(() => {
  driversBolt.value = [
    { Kierowca: "", Gotówka: 0, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 },
  ];
  driversUber.value = [
    { Kierowca: "", Gotówka: 999, Przelew: 0, Bonus: 0, Napiwki: 0, Utarg: 0 },
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
.input-container {
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
  margin-top: 16px;
  display: flex;
  justify-content: space-around;
}
.btn {
  margin-top: 16px;
  border: solid black 1px;
  border-radius: 4px;
  padding: 8px 12px;
}
.btn:hover {
  cursor: pointer;
  opacity: 0.6;
}
.section {
  display: flex;
  padding: 32px;
  flex-direction: column;
}
.loaded {
  background-color: green;
  color: white;
}
table {
  width: 100%; /* Aby tabela rozciągała się na całą dostępną szerokość */
  border-collapse: collapse; /* Usuwa podwójne ramki między komórkami */
}

table,
th,
td {
  border: 1px solid black; /* Dodaje ramki do tabeli, nagłówków i komórek */
}

th,
td {
  width: 200px; /* Ustawia szerokość każdej kolumny */
  height: 40px; /* Ustawia wysokość każdego wiersza */
  text-align: center; /* Wyśrodkowanie tekstu w poziomie */
  vertical-align: middle; /* Wyśrodkowanie tekstu w pionie */
}
.menu {
  margin-top: 16px;
}
.link {
  padding: 4px;
}
</style>
