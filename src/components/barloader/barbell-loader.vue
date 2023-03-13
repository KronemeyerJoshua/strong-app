<template>
<div id="container">
  <div id="weights">
    <h1>Weight: {{totalWeight}}lbs | {{totalWeightKg}}kgs</h1>
    <div v-if="!isKg" class="weight-plate-selection-lbs" >
      <div v-for="i in plateWeightsLbs" :key="i">
        <div @click="addWeight(i)" :class="'lbs-plate-'+i">{{i}}</div>
      </div>
    </div>
    <div v-if="isKg" class="weight-plate-selection-kgs" >
      <div v-for="i in plateWeightsKgs" :key="i">
        <div @click="addWeight(i)" :class="'kgs-plate-'+i">{{i}}</div>
      </div>
    </div>
  </div>

  <div id="bar-loader">
    <div v-for="i in maximumPlatesSide" :key="'l-'+i" :class="['plate', 'plate-'+(this.maximumPlatesSide+1 - i), maximumPlatesSide-plates.length < i ? 'active' : '']"></div>
    <div class="bar"></div>
    <div v-for="i in maximumPlatesSide" :key="'r-'+i"  :class="['plate', 'plate-'+i, plates.length > i-1 ? 'active' : '']"></div>
  </div>
  <ion-item>
    <ion-label>Kilograms</ion-label>
    <ion-toggle @click="enableKg" color="success"></ion-toggle>
  </ion-item>
  <div @click="removeWeight" style="text-align: center; margin: 0.2rem;">Remove Plate</div>
</div>
</template>

<script>
import {defineComponent} from "vue";
import {Decimal} from 'decimal.js';
import {IonToggle, IonLabel, IonItem} from "@ionic/vue";

export default defineComponent( {
  name: "barbell-loader",
  components: {
    IonToggle,
    IonLabel,
    IonItem
  },
  data() {
    return {
      plateWeightsLbs: [100,55,45,35,25,10,5,2.5],
      plateWeightsKgs: [25,20,15,10,5,2.5,1.25],
      plateHeightDict: {
        110: "height: 80%",
        100: "height: 80%",
        55: "height: 80%",
        45: "height: 80%",
        44: "height: 80%",
        35: "height: 70%",
        33: "height: 70%",
        25: "height: 60%",
        22: "height: 60%",
        11: "height: 50%",
        10: "height: 50%",
        5.5: "height: 40%",
        5: "height: 40%",
        2.75: "height: 30%",
        2.5: "height: 30%"
      },
      plateColorDictKgs: {
        110: "height: 80%",
        55: "red",
        44: "blue",
        33: "yellow",
        22: "green",
        11: "white",
        5.5: "black",
        2.75: "grey"
      },
      plates: [],
      plateIndex: 0,
      maximumPlatesSide: 8,
      totalWeight: 45,
      isKg: false
    }
  },
  computed: {
    totalWeightKg() {
      return (this.totalWeight / 2.2).toFixed(2)
    }
  },
  methods: {
  addWeight(weight) {
    weight = new Decimal(weight);
    let reAddWeight = [];
    let reAddStyle = [];
    if (this.plates.length === this.maximumPlatesSide)
      return;
    if (this.isKg)
      weight = weight.times(2.2)

    for (let i = this.plates.length-1; i >= 0; i--)
    {
      if (weight.greaterThan(this.plates[i]))
      {
        console.log(weight + " > " + this.plates[i])
        let el = Array.from(document.getElementsByClassName('plate-' + this.plates.length));
        reAddStyle.push(`height: ${el[0].style.height}; background-color: ${el[0].style.backgroundColor};`);
        reAddWeight.push(this.plates[i])
        this.removeWeight()
      }
    }

    console.log("Adding weight: " + weight * 2);
    this.plates.push(weight);
    Array.from(document.getElementsByClassName('plate-' + this.plates.length)).forEach( (el) => {
      el.style = this.plateHeightDict[weight];
      if (this.isKg)
      {
        el.style.backgroundColor = this.plateColorDictKgs[weight];
      }
        }
    )
    let sumAdditionalWeight = new Decimal(reAddWeight.reduce((a,b) => +a + +b, 0)).times(2);
    console.log("Weight Taken Off: " + sumAdditionalWeight)
    this.totalWeight = weight.times(2).plus(this.totalWeight).plus(sumAdditionalWeight);

    for (let i = reAddWeight.length-1; i >= 0; i--)
    {
      console.log(reAddWeight.length)
      console.log("readding weight: " + reAddWeight[i])
      console.log(reAddStyle[i]);
      this.plates.push(reAddWeight[i]);
      Array.from(document.getElementsByClassName('plate-' + this.plates.length)).forEach( (el) => {
            el.style = reAddStyle[i]
          }
      )
    }
  },
  removeWeight() {
    if (this.plates.length < 1)
      return;

    let weight = this.plates.pop() * 2
    console.log("Removing weight: " + weight)
    this.totalWeight -= weight;
  },
    enableKg() {
      this.isKg = !this.isKg;
      for (let i = 0; i < this.plateWeightsKgs.length; i++)
      {
        console.log(new Decimal(this.plateWeightsKgs[i]).times(2.2).toString())
      }
    }
}
});
</script>

<style scoped>
.weight-plate-selection-lbs {
  display: flex;
  flex-wrap: wrap;
}
.weight-plate-selection-kgs {
  display: flex;
  flex-wrap: wrap;
}
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#weights [class^="lbs-plate-"] {
  height: 50px;
  width: 50px;
  background-color: #555;
  border-radius: 50%;
}

#weights [class^="kgs-plate-"] {
  height: 50px;
  width: 50px;
  background-color: #555;
  border-radius: 50%;
}

.active {
  display: block !important;
}
#bar-loader {
  height: 8rem;
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
}

#bar-loader .plate {
  width: 3%;
  height: 80%;
  background-color: black;
  border: solid 1px white;
  display: none;
}

#bar-loader .bar {
  width: 45%;
  height: 1rem;
  background-color: grey;
}
</style>