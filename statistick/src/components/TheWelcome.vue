<template>
  <div>
    <div>
      <p>68,66,68,71,71,71,74,74,76,78</p>
      <p>66,66,68,69,70,73,73,74,76,78</p>
      <label>
        Введите ряд через запятую:
        <input v-model="raw" type="text" @change="display" />
      </label>
    </div>
    <div v-if="show">
      <table>
        <tr>
          <td v-for="value in ar">{{ value }}</td>
        </tr>
      </table>
      <table>
        <tr>
          <td>N</td>
          <td v-for="n in ar.length">{{ n }}</td>
        </tr>
        <tr>
          <td>Xi</td>
          <td v-for="value in ar">{{ value }}</td>
        </tr>
        <tr>
          <td>Возр.</td>
          <td v-for="value in sorted">{{ value }}</td>
        </tr>
      </table>
      <p>
        n = {{ ar.length }} <span>k = {{ k }}</span> h = {{ h }}
      </p>
      <table>
        <tr>
          <th>N</th>
          <th>Граница интервала</th>
          <th>Частота</th>
          <th>Накопленная частота</th>
          <th>Частность</th>
          <th>Накопленная частость</th>
          <th>Середина интервала</th>
        </tr>
        <tr v-for="el in intervals">
          <td>{{ el.n }}</td>
          <td>{{ el.low }} - {{ el.hight }}</td>
          <td>{{ el.chast }}</td>
          <td>{{ el.nakChast }}</td>
          <td>{{ el.chastot }}</td>
          <td>{{ el.nakChastotnost }}</td>
          <td>{{ el.ser }}</td>
        </tr>
      </table>
      <p>Mo = {{ Mo }}  Me = {{ Me }} Sr = {{ sr }}  S2 = {{ S2 }}  S = {{ S }}</p>
      <p>V = {{ V }}%  <span v-if="V<10">Коэффициент вариации небольшой</span><span v-if="V>=10 && V <20">Коэффициент вариации средний</span><span v-if="V>20">Коэффициент вариации большой</span></p>
      <p>Стандартная ошибка среднего арифметического = {{ stError }}</p>
      <p>Мера скошенности = {{ merSkosh }} <span v-if="merSkosh>=0">Асимметрия левосторонняя</span><span v-if="merSkosh<0">Асимметрия правосторонняя</span></p>
      <p>Эксцесс = {{ exces }} <span v-if="exces>0">Островершинное распределение</span><span v-if="exces<0">Плосковершинное распределение</span><span v-if="exces==0">Средневершинное распределение</span></p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      raw: null,
      rawN: [],
      show: false,
      ar: null,
      sorted: null,
      k: 0,
      h: 0,
      maxChast: 0,
      maxChastot: 0,
      intervals: [],
      modInterval: null,
      beforMod: null,
      pastMod: null,
      medInterval: null,
      beforMed: null,
      pastMed: null,
      nullEnt: {
        n: 0,
        chast: 0,
        nakChast: 0,
        chastot: 0,
        nakChastotnost: 0,
        ser: 0,
        low: 0,
        hight: 0,
      },
      chastota: 0,
      chastotnost: 0,
      Mo:0,
      Me:0,
      sr: 0,
      sum:0,
      S2:0,
      S:0,
      sumS2:0,
      sumExcess:0,
      V: 0,
      stError: 0,
      merSkosh: 0,
      exces:0,
    };
  },
  methods: {
    display() {
      this.show = true;
      this.start();
    },
    start() {
      this.ar = this.raw.split(",");
      this.ar.forEach((e) => {
        this.rawN.push(Number(e));
      });
      this.sorted = this.rawN;
      this.sorted.sort(function (a, b) {
        return a - b;
      });
      this.ar = this.raw.split(",");
      if (this.ar.length < 8) {
        this.k = 3;
      } else if (this.ar.length < 16) {
        this.k = 4;
      } else {
        this.k = 5;
      }
      this.h = (this.sorted[this.sorted.length - 1] - this.sorted[0]) / this.k;
      for (let i = 1; i < this.k + 1; i++) {
        let obj = {};
        obj.n = i;
        obj.low = this.sorted[0] + (i - 1) * this.h;
        obj.hight = this.sorted[0] + i * this.h;
        obj.chast = 0;
        this.sorted.forEach((e) => {
          if (e >= obj.low && e < obj.hight) {
            obj.chast++;
          }
          if (
            e == obj.hight &&
            obj.hight == this.sorted[this.sorted.length - 1]
          ) {
            obj.chast++;
          }
        });
        obj.nakChast = this.maxChast + obj.chast;
        this.maxChast += obj.chast;
        obj.chastot = obj.chast / this.sorted.length;
        obj.nakChastotnost = this.maxChastot + obj.chastot;
        obj.ser = obj.low + this.h / 2;
        this.maxChastot += obj.chastot;
        this.intervals.push(obj);
      }
      this.intervals.forEach((e) => {
        if (e.chast >= this.chastota) {
          this.chastota = e.chast;
          this.modInterval = e;
          this.beforMod = this.intervals[e.n - 2];
          this.pastMod = this.intervals[e.n];
        }
        if (e.nakChastotnost >= 0.5 && this.medInterval == null) {
          this.medInterval = e;
          this.beforMed = this.intervals[e.n - 2];
          this.pastMed = this.intervals[e.n];
        }
      });
      if (this.pastMod == undefined) {
        this.pastMod = this.nullEnt;
      }
      if (this.beforMod == undefined) {
        this.pastMod = this.nullEnt;
      }
      if (this.pastMed == undefined) {
        this.pastMod = this.nullEnt;
      }
      if (this.beforMed == undefined) {
        this.pastMod = this.nullEnt;
      }
      this.Mo = this.modInterval.low + (this.h*(this.modInterval.chast-this.beforMod.chast))/(this.modInterval.chast -this.beforMod.chast + this.modInterval.chast - this.pastMod.chast)
      this.Me = this.medInterval.low + (this.h*(0.5*this.sorted.length-this.beforMed.nakChast)/this.medInterval.chast)
      this.sorted.forEach((e) =>{
        this.sum +=e
      })
      this.sr = this.sum/this.sorted.length
      this.intervals.forEach((e) => {
        this.sumS2 += e.chast*((e.ser - this.sr)**2)
        this.sumExcess += e.chast*((e.ser - this.sr)**4)
      })
      this.S2 = (1 / (this.sorted.length - 1)) * this.sumS2
      this.S = Math.sqrt(this.S2)
      this.V = (this.S/this.sr) * 100
      this.stError = (this.S/Math.sqrt(10))
      this.merSkosh = (this.sr - this.modInterval.low)/this.S
      this.exces = ((this.sumExcess/(this.sorted.length * this.S**4)) - 3)
    },
  },
};
</script>

<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
}
td {
  border: 1px solid black;
  padding: 3px;
}
</style>