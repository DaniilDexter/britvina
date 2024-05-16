<template>
  <div>
    <div>
      <p>X: 92,77,88,98,90,92,78,82,85,91,90,85,92,93,87,79,96,98,78,90</p>
      <p>Y: 91,70,86,76,76,69,78,81,83,79,76,68,86,90,79,70,71,83,76,88</p>
      <label>
        Введите значения Х через запятую:
        <input type="text" v-model="x" />
      </label>
      <label>
        Введите значения Y через запятую:
        <input type="text" v-model="y" />
      </label>
    </div>
    <br>
    <div v-if="x != null && y != null">
      <table>
        <tr>
          <td>Xi</td>
          <td v-for="value in xArray">{{ value }}</td>
        </tr>
        <tr>
          <td>Yi</td>
          <td v-for="value in yArray">{{ value }}</td>
        </tr>
      </table>
      <br>
      <div class="container">
        <table>
          <tr>
            <th>N</th>
            <th>X</th>
            <th>Y</th>
            <th>X^2</th>
            <th>Y^2</th>
            <th>X*Y</th>
          </tr>
          <tr v-for="el in lines">
            <td>{{ el.n }}</td>
            <td>{{ el.x }}</td>
            <td>{{ el.y }}</td>
            <td>{{ el.x2 }}</td>
            <td>{{ el.y2 }}</td>
            <td>{{ el.xy }}</td>
          </tr>
          <tr>
            <td>Сумма</td>
            <td>{{ sumX }}</td>
            <td>{{ sumY }}</td>
            <td>{{ sumX2 }}</td>
            <td>{{ sumY2 }}</td>
            <td>{{ sumXY }}</td>
          </tr>
        </table>
        <div>
          <p>Xср = {{ xSr }};   (Xср)^2 = {{ xSr2 }}</p>
          <p>Yср = {{ ySr }};   (Yср)^2 = {{ ySr2 }}</p>
          <p>Xср * Yср = {{ xSrYsr }}</p>
          <br>
          <p>𝝈𝒙 ^2 = {{ dispX }}; 𝝈𝒙 = {{ sqoX }}</p>
          <p>𝝈𝒚 ^2 = {{ dispY }}; 𝝈𝒚 = {{ sqoY }}</p>
          <br>
          <p>μ = {{ mu }}</p>
          <p>r = {{ r }}</p>
          <br>
          <p>By/x = {{ b }}</p>
          <p>Ay/x = {{ a }}</p>
          <br>
          <p>y = {{ a }} + {{ b }}x</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      x: null,
      y: null,
      arrayPerebor: null,
      xArray: [],
      yArray: [],
      lines: [],
      sumX: 0,
      sumY: 0,
      sumX2: 0,
      sumY2: 0,
      sumXY: 0,
      xSr:0,
      ySr:0,
      xSr2:0,
      ySr2:0,
      xSrYsr:0,
      sqoX:0,
      sqoY:0,
      dispX:0,
      dispY:0,
      mu:0,
      r:0,
      a:0,
      b:0,
    };
  },
  methods: {
    calculate() {
      this.arrayPerebor = this.x.split(",");
      this.arrayPerebor.forEach((e) => {
        this.xArray.push(Number(e));
      });
      this.arrayPerebor = this.y.split(",");
      this.arrayPerebor.forEach((e) => {
        this.yArray.push(Number(e));
      });
      for (let i = 0; i < this.xArray.length; i++) {
        let obj = {};
        obj.n = i + 1;
        obj.x = this.xArray[i];
        obj.y = this.yArray[i];
        obj.x2 = this.xArray[i] * this.xArray[i];
        obj.y2 = this.yArray[i] * this.yArray[i];
        obj.xy = this.xArray[i] * this.yArray[i];
        this.lines.push(obj);
      }
      this.lines.forEach((e) => {
        this.sumX += e.x;
        this.sumY += e.y;
        this.sumX2 += e.x2;
        this.sumY2 += e.y2;
        this.sumXY += e.xy;
      });
      this.xSr = this.sumX / this.xArray.length
      this.ySr = this.sumY / this.yArray.length
      this.xSr2 = this.xSr**2
      this.ySr2 = this.ySr**2
      this.xSrYsr = this.xSr * this.ySr
      this.dispX = (this.sumX2/this.xArray.length) - this.xSr2
      this.dispY = (this.sumY2/this.xArray.length) - this.ySr2
      this.sqoX = Math.sqrt(this.dispX)
      this.sqoY = Math.sqrt(this.dispY)
      this.mu = (this.sumXY/this.xArray.length) - this.xSrYsr
      this.r = this.mu/(this.sqoX*this.sqoY)
      this.b = this.r/Math.sqrt(this.sqoY/this.sqoX)
      this.a = this.ySr - (this.b*this.xSr)
    },
  },
  computed: {
    start() {
      if (this.x != null && this.y != null) {
        return true;
      } else {
        return false;
      }
    },
  },
  watch: {
    start() {
      this.calculate();
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

.container {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  width: 100%;
}
</style>