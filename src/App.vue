<template>
  <div>
    <header class="header-covid">
      <div><img src="" /> COVID-19 TRACKER</div>
    </header>
    <img
      alt="Vue logo"
      style="width: 300px"
      src="https://c.tenor.com/Aqe8V3wni4QAAAAC/covid19-coronavirus.gif"
    />

    <h1 style="text-align: center">สถานการณ์โควิดวันนี้</h1>
    <h3>เวลา ณ ปัจจุบัน : {{ dataDate }}</h3>
    <div class="grid">
      <div class="item">
        <div class="content">
          <h2>Cases</h2>
          <p>
            ผู้ติดเชื้อรายใหม่วันนี้ <b>{{ numberWithCommas(stats.NewConfirmed) }}</b>
          </p>
          <p>
            รวม <b>{{ numberWithCommas(stats.TotalConfirmed) }}</b>
          </p>
        </div>
      </div>
      <div class="item">
        <div class="content">
          <h2>Deaths</h2>

          <p>
            ยอดผู้เสียชีวิตรายใหม่วันนี้
            <b> {{ numberWithCommas(stats.NewDeaths) }}</b>
          </p>
          <p>
            รวม <b> {{ numberWithCommas(stats.TotalDeaths) }}</b>
          </p>
        </div>
      </div>
    </div>

    <div
      @input="filter()"
      style="padding-top: 20px; padding-bottom: 10px; width: 100%"
    >
      <h3>
        ค้นหา : <input type="text"  v-model="search" placeholder="ชื่อประเทศ"  />
      </h3>
      <p class="error-message" v-if="DisplayCountry.length < 1">
    ไม่เจอข้อมูลในส่วนนี้
</p>
    </div>

    <div class="card">
      <div class="covid-table">
        <table>
          <tr>
            <td class="t1">ประเทศ</td>
            <td class="t1" v-on:click="sortMin()">ผู้ติดเชื้อในประเทศ</td>
            <td class="t1" v-on:click="sortMax()">ผู้เสียชีวิตในประเทศ</td>
          </tr>
          <tr v-for="(value, index) in DisplayCountry" :key="'DisplayCountry' + index">
            <td>{{ value.Country }}</td>
            <td>{{ numberWithCommas(value.TotalConfirmed) }}</td>
            <td>{{ numberWithCommas(value.TotalDeaths) }}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: "App",
  component: {
   
  },

  data() {
    return {
      dataDate: "",
      stats: {},
      countries: [],
      search: "",
      isActive: false,
      DisplayCountry: [],
    };
  },

  methods: {
    async fetchCovidData() {
      const res = await fetch("https://api.covid19api.com/summary");
      const data = await res.json();
      // console.log(data)
      return data;
    },
    filter() {
      // console.log(this.countries.filter((x) =>x["Country"].includes(this.search)))
      // console.log(this.DisplayCountry);
      this.DisplayCountry= this.countries.filter((x) =>
        x["Country"].match(this.search)
      );
      
    },
    
    sortMin() {
      this.DisplayCountry.sort((a, b) => a["TotalConfirmed"] - b["TotalConfirmed"]);
      this.DisplayCountry.sort((a, b) => a["TotalDeaths"] - b["TotalDeaths"]);
    },
    sortMax() {
      this.DisplayCountry.sort((a, b) => b["TotalDeaths"] - a["TotalDeaths"]);
      this.DisplayCountry.sort((a, b) => b["TotalConfirmed"] - a["TotalConfirmed"]);
    },
  
  },

  async created() {
    const data = await this.fetchCovidData();
    // console.log({data});
    this.dataDate = data.Date;
    this.stats = data.Global;
    this.countries = data.Countries;
    this.DisplayCountry = this.countries;
  },

  setup() {
    return {
      numberWithCommas(x) {
        try {
          return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        } catch (ex) {
          return "";
        }
      },
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.covid-table {
  text-align: left;
  border: #2c3e50;

  border: 1px solid rgb(209, 209, 209);
  display: table;
  margin: 0 auto;
  width: auto;
}
.covid-table td {
  padding-left: 10px;
  border-collapse: collapse;
  padding-bottom: 2px;
}
.card {
  padding-top: 10px;
  padding-right: 30px;
  padding-left: 30px;
}
.t1 {
  background-color: rgba(28, 236, 56, 0.226);
  font-size: 15px;
  font-size: bold;
  padding: 5px;
  border-collapse: collapse;
  font-weight: bold;
}

.b {
  padding-left: 10px;
  padding-right: 10px;
}
.header-covid {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  font-size: 30px;
  background-color: rgba(28, 236, 56, 0.226);
  padding: 1%;
}
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  justify-items: stretch;
  align-items: stretch;
  column-gap: 20px;
  row-gap: 20px;
  padding-top: 2%;
  padding-left: 10%;
  padding-right: 10%;
}

.content {
  color: #242424;
  background-color: rgba(28, 236, 56, 0.226);
  font-weight: 600;
  text-align: center;
  box-sizing: border-box;
  height: 100%;
  padding: 3%;
  padding-bottom: 30px;
  border-radius: 5%;
}
.btn-1 {
  margin-left: 10px;
}
.error-message{
  color: rgb(255, 0, 0);
}
</style>
