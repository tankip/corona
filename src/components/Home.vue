<template>
  <div id="home">
    <b-card
      v-if="summary.updated"
      bg-variant="light"
      class="summary"
      text-variant="black"
      title="World Summary"
    >
    <hr />
      <b-card-text>
        <span class="text-center">{{ summary.updated ? formatDate(summary.updated) : 0 }}</span>

        <div class="row">
          <div class="col-md-6">
            <pie-chart class="chart" :data="chartData" :options="chartOptions" :styles="myStyles"></pie-chart>
          </div>
          <div class="col-md-6 tex-box">
            <b-card-text class="move-down">
              As of now
              <strong class="cases">{{ summary.cases ? summary.cases.toLocaleString() : 0 }}</strong>
              total cases have been reported,
              <strong
                class="dead"
              >{{ summary.deaths ? summary.deaths.toLocaleString() : 0 }}</strong>
              patients have died and
              <strong
                class="recover"
              >{{ summary.recovered ? summary.recovered.toLocaleString() : 0 }}</strong> patients have recovered.
            </b-card-text>
            <br />
            <div class="text-center">
              <b-badge pill variant="dark" class="my-info">Social Distance</b-badge>
              <b-badge pill variant="dark" class="my-info">Wash Hands</b-badge>
              <b-badge pill variant="dark" class="my-info">Stay Home</b-badge>
              <b-badge pill variant="dark" class="my-info">Stay Safe</b-badge>
            </div>
          </div>
        </div>
      </b-card-text>
    </b-card>
    <div class="row">
      <div class="col-md-12 country-search">
        <b-form-input v-model="countryName" placeholder="Search by country name"></b-form-input>
      </div>
    </div>

    <div class="row countries" v-if="countries.length">
      <div
        class="col-sm-12 col-md-6 col-lg-4"
        v-for="(country, key) in filteredCountries"
        :key="key"
      >
        <b-card img-top tag="article" class="mb-2">
          <div class="mb-2">
            <span class="country-title">
              <u>{{ country.country ? formatText(country.country) : ' - ' }}</u>
            </span>
            <b-avatar
              variant="light"
              class="float-right align-top"
              :src="country.countryInfo.flag"
              size="lg"
            ></b-avatar>
          </div>
          <b-card-text>
            <span class="country-num cases">{{ country.cases ? country.cases.toLocaleString() : 0 }}</span> total cases reported
          </b-card-text>
          <b-card-text>
            <span class="country-num dead">{{ country.deaths ? country.deaths.toLocaleString() : 0 }}</span> total dead
          </b-card-text>
          <b-card-text>
            <span class="country-num recover">{{ country.recovered ? country.recovered.toLocaleString() : 0 }}</span> patients have recovered
          </b-card-text>
          <b-card-text>
            <span class="country-num critical">{{ country.critical ? country.critical.toLocaleString() : 0 }}</span> are in critial condition
          </b-card-text>
         
          <b-card-text>
            <span class="country-num new">{{ country.todayCases ? country.todayCases.toLocaleString() : 0 }}</span> new cases today
          </b-card-text>

          <b-card-text>
            <span class="country-num dead">{{ country.todayDeaths ? country.todayDeaths.toLocaleString() : 0 }}</span> have died today
          </b-card-text>
        
          <b-card-text>
            <span
              class="country-num"
            >{{ country.casesPerOneMillion ? country.casesPerOneMillion.toLocaleString() : 0 }}</span> cases per one million
          </b-card-text>
          <b-card-text>
            <span
              class="country-num death-per"
            >{{ country.deathsPerOneMillion ? country.deathsPerOneMillion.toLocaleString() : 0 }}</span> deaths per one million
          </b-card-text>
        </b-card>
      </div>
    </div>
    <p v-if="loading">Please wait...</p>
    <p v-if="error">{{ error }}</p>
  </div>
</template>

<script>
import axios from "axios";
import PieChart from "./PieChart.vue";
export default {
  name: "Home",
  components: {
    PieChart
  },
  data: () => {
    return {
      countries: [],
      summary: {},
      countryName: "",
      error: "",
      loading: false,
      chartOptions: {
        hoverBorderWidth: 40,
        responsive: true,
        maintainAspectRatio: false,
        cutoutPercentage: 50,
        animation: {
          animateRotate: true
        }
      },
      myStyles: {
        position: "relative",
        height: "250px",
        width: "250px",
        margin: "auto"
      },
      chartData: {
        hoverBackgroundColor: "red",
        hoverBorderWidth: 20,
        labels: ["Reported", "Deaths", "Recovered"],
        datasets: [
          {
            label: "Data One",
            backgroundColor: ["dodgerblue", "red", "limegreen"],
            data: []
          }
        ]
      }
    };
  },
  mounted() {
    this.error = "";
    this.getSummary();
    this.getCountries();
  },
  methods: {
    formatText(text) {
      let arr = text.split(",");
      return arr[0];
    },
    formatDate(date) {
      return new Date(date).toTimeString();
    },
    getCountries() {
      this.error = "";
      this.loading = true;
      axios
        .get("https://corona.lmao.ninja/v2/countries?sort=country")
        .then(response => {
          this.loading = false;
          this.countries = response.data.reverse();
        })
        .catch(() => {
          this.loading = false;
          this.err = "There was an error getting the cases.";
        });
    },
    getSummary() {
      axios
        .get("https://corona.lmao.ninja/v2/all")
        .then(response => {
          this.summary = response.data;
          this.chartData.datasets[0].data[0] = this.summary.cases;
          this.chartData.datasets[0].data[1] = this.summary.deaths;
          this.chartData.datasets[0].data[2] = this.summary.recovered;
        })
        .catch(() => {});
    }
  },
  computed: {
    filteredCountries() {
      if (this.countries.length) {
        return this.countries.filter(value => {
          return value.country
            .toLowerCase()
            .includes(this.countryName.toLowerCase());
        });
      } else {
        return [];
      }
    }
  }
};
</script>

<style>
#home {
  margin-top: 16px;
}
.my-info {
  margin-right: 5px;
}
@media (max-width: 768px) {
  .move-down {
    margin-top: 16px;
  }
}

.tex-box {
  margin: auto;
}
.dead {
  color: red;
  font-size: 20px;
}
.cases {
  color: dodgerblue;
  font-size: 20px;
}
.recover {
  color: limegreen;
  font-size: 20px;
}
.critical {
  color: orangered;
}
.new {
  color: purple;
}
.death-per {
  color: orange;
}
.country-title {
  font-size: 26px;
}
.country-num {
  font-size: 24px;
  font-weight: 800;
}
p {
  margin-bottom: 0px !important;
}
.summary {
  text-align: center;
  margin-bottom: 16px !important;
}
.chart {
  margin-left: 100px auto;
}
.card {
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  margin-bottom: 30px;
}

.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

.country-search {
  margin-bottom: 16px !important;
}
.form-control {
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  background-color: #e8eaed !important;
  color: black !important;
}
</style>