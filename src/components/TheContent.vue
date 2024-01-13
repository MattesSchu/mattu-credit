<script setup lang="ts">
import { ref, onMounted, watch } from "vue";

const startGeld = ref(50000);
const monatlichZurücklegen = ref(1000);
const erwarteteZinsenImJahr = ref(4);
const zeitraumInJahren = ref(2);

const total = ref<number>(0);
const totalIncome = ref<number>(0);
const totalZins = ref<number>(0);

watch(startGeld, () => estimatedMoneyInMonths());
watch(monatlichZurücklegen, () => estimatedMoneyInMonths());
watch(erwarteteZinsenImJahr, () => estimatedMoneyInMonths());
watch(zeitraumInJahren, () => estimatedMoneyInMonths());

function estimatedMoneyInMonths(): number[][] {
    let res: number[][] = [];
    let current = startGeld.value;
    let zinsAllein = 0;
    let sparenAllein = 0;
    for (let year = 0; year < zeitraumInJahren.value; year++) {
        let monthsForYear: number[] = [];
        for (let month = 0; month < 12; month++) {
            current += monatlichZurücklegen.value;
            const currentZins = (current * (erwarteteZinsenImJahr.value / 12)) / 100;
            zinsAllein += currentZins;
            sparenAllein += monatlichZurücklegen.value;
            current += currentZins;
            current = Math.floor(current);
            monthsForYear.push(current);
        }
        res.push(monthsForYear);
    }

    totalIncome.value = sparenAllein;
    totalZins.value = Math.floor(zinsAllein);
    total.value = current;

    return res;
}
function withDots(n: number | undefined): string {
    if (!n) {
        return "...";
    }
    let res = "";
    const nStr = n.toString();
    let j = 0;
    for (let i = nStr.length - 1; i >= 0; i--) {
        res = nStr[i] + res;
        if ((j + 1) / 3 === 1) res = "." + res;
        j++;
    }
    return res;
}

onMounted(() => {
    estimatedMoneyInMonths();
});
</script>
<template>
    <h2>Angaben</h2>
    <div class="containerParameter">
        <div class="item">
            <h3>Startkapital in €</h3>
            <input type="number" step="1000" v-model="startGeld" />
        </div>
        <div class="item">
            <h3>Sparen in €</h3>
            <input type="number" step="100" v-model="monatlichZurücklegen" />
        </div>
        <div class="item">
            <h3>Erwartete Zinsen in %</h3>
            <input type="number" step=".1" v-model="erwarteteZinsenImJahr" />
        </div>
        <div class="item">
            <h3>Jahre</h3>
            <input type="number" min="1" max="30" v-model="zeitraumInJahren" />
        </div>
    </div>
    <h2>Ergebnis</h2>
    <div class="item ergebnis">
        <table>
            <thead>
                <tr>
                    <th>Posten</th>
                    <th>Ergebnis</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Startkapital</td>
                    <td>{{ withDots(startGeld) }}&#8202;€</td>
                </tr>
                <tr>
                    <td>Sparen</td>
                    <td>{{ withDots(totalIncome) }}&#8202;€</td>
                </tr>
                <tr>
                    <td>Zinsen</td>
                    <td>{{ withDots(totalZins) }}&#8202;€</td>
                </tr>
                <tr>
                    <td>Insgesamt</td>
                    <td>{{ withDots(total) }}&#8202;€</td>
                </tr>
            </tbody>
        </table>
        <div class="bargraph">
            <div class="barGraphItem barStartkapital">Startkapital</div>
            <div class="barGraphItem barSparen">Sparen</div>
            <div class="barGraphItem barZinsen">Zinsen</div>
        </div>
    </div>
    <h2>Jahresübersicht</h2>
    <div class="item">
        <div class="overview">
            <div class="yearItem" v-for="(year, yearIdx) in estimatedMoneyInMonths()">
                <h3>Jahr {{ yearIdx + 1 }}</h3>
                <table>
                    <tr>
                        <th>Monat</th>
                        <th>Geld</th>
                    </tr>
                    <tr v-for="(month, monthIdx) in year">
                        <td>{{ monthIdx + 1 }}</td>
                        <td>{{ withDots(month) }}€</td>
                    </tr>
                </table>
            </div>
        </div>
    </div>
</template>

<style scoped>
.containerParameter {
    padding: 2px;

    background-color: rgb(6, 80, 80);
    border: 2px solid rgb(8, 90, 90);
    border-radius: 5px;

    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
}

.item {
    padding: 10px 10px;
    background-color: rgb(49, 48, 53);
    border: 1px solid rgb(75, 73, 82);
    border-radius: 5px;
    margin-top: 10px;
    margin-bottom: 10px;
}

.bargraph {
    color: lightgray;
    width: 100%;
    display: grid;
    text-align: center;
    grid-template-columns: v-bind('"" + 100 * (startGeld / total) + "%"') v-bind(
            '"" + 100 * (totalIncome / total) + "%"'
        ) v-bind('"" + 100 * (totalZins / total) + "%"');
    .barStartkapital {
        background-color: rgb(33, 41, 156);
    }
    .barSparen {
        background-color: rgb(160, 81, 24);   
    }
    .barZinsen {
        background-color: rgb(0, 138, 143);
    }
}

.barGraphItem {
    min-height: 100px;
    border: 1px solid white;
    border-radius: 5px;
}

.ergebnis {
    display: grid;
    gap: 10px;
    grid-template-columns: auto;
    table {
        border: 1px solid white;
        border-radius: 5px;
        padding: 0px 10px 5px 10px;
        justify-self: center;
        max-width: 500px;
    }
    th {
        text-decoration: underline;
    }
    td {
        padding: 1px 5px;
        text-align: center;
    }
}

.overview {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.yearItem {
    margin: 10px;
    padding: 10px;
    border-radius: 5px;

    background-color: rgb(24, 0, 56);
    border: 1px solid white;
    padding: 10px;

    h3 {
        text-align: center;
        text-decoration: underline;
    }

    td {
        padding: 1px 5px;
        text-align: right;
    }
}
</style>
