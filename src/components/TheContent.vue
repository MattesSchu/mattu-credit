<script setup lang="ts">
import { ref } from 'vue'

const startGeld = ref(50000);
const monatlichZurücklegen = ref(1000);
const erwarteteZinsenImJahr = ref(4);
const zeitraumInJahren = ref(2);

const showMonths = ref(false);

const total = ref<number>();
const totalZins = ref<number>();

// Matti
// 25,582
// Malu
// 42,124

function estimatedMoneyInMonths(): number[][] {
  let res: number[][] = []
  let current = startGeld.value;
  let zinsAllein = 0;
  for (let year = 0; year < zeitraumInJahren.value; year++) {
    let monthsForYear: number[] = [];
    for (let month = 0; month < 12; month++) {
      current += monatlichZurücklegen.value

      const currentZins = (current * (erwarteteZinsenImJahr.value / 12)) / 100;
      zinsAllein += currentZins;
      current += currentZins;
      current = Math.floor(current)
      monthsForYear.push(current)
    }
    res.push(monthsForYear);
  }
  totalZins.value = Math.floor(zinsAllein);
  total.value = current;
  return res;
}
</script>
<template>
  <h2>TheContent</h2>
  <div class="containerParameter">
    <div class="item">
      <h3>Start</h3>
      <input type="number" step="1000" v-model="startGeld" />
    </div>
    <div class="item">
      <h3>Monatlich zurücklegen</h3>
      <input type="number" step="100" v-model="monatlichZurücklegen" />
    </div>
    <div class="item">
      <h3>Erwartete Zinsen</h3>
      <input type="number" step=".1" v-model="erwarteteZinsenImJahr" />
    </div>
    <div class="item">
      <h3>Jahre</h3>
      <input type="number" v-model="zeitraumInJahren" />
    </div>
    <div class="item">
      <h3>Berechnen</h3>
      <button @click="estimatedMoneyInMonths">Go</button>
    </div>
  </div>
  <div class="item">
    <h3>Ergebnis</h3>
    <p>
      Insgesamt: {{ total }}€
    </p>
    <p>
      davon Zinsen: {{ totalZins }}€
    </p>
    <label for="showMonths">Einzelne Monate Anzeigen</label>
    <input type="checkbox" id="showMonths" v-model="showMonths"/>
    <div class="overview" v-if="showMonths">
      <div class="yearItem" v-for="(year, yearIdx) in estimatedMoneyInMonths()">
        <h3>Jahr {{ yearIdx+1 }}</h3>
        <table>
          <tr>
            <th>Monat</th>
            <th>Geld</th>
          </tr>
          <tr v-for="(month, monthIdx) in year">
            <td>{{ monthIdx+1 }}</td>
            <td>{{ month }}€</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
h2 {
  margin-bottom: 10px;
}

.containerParameter {
  padding: 2px;

  background-color: rgb(6, 80, 80);
  border: 2px solid rgb(8, 90, 90);
  border-radius: 5px;

  display: flex;
  justify-content: center;
  gap: 10px;
}

.item {
  padding: 5px 10px;
  background-color: rgb(49, 48, 53);
  border: 1px solid rgb(75, 73, 82);
  border-radius: 5px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.overview {
  display: flex;
}

.yearItem {
  margin: 10px;
  padding: 10px;
  border-radius: 5px;

  background-color: rgb(24, 0, 56);
  border: 1px solid white;
  padding: 10px
}

td {
  padding: 1px 5px;
  text-align: right;
}
</style>
