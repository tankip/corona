<template>
  <div id="home">
    <b-card
      v-if="summary"
      bg-variant="dark"
      class="country-search"
      text-variant="white"
      title="World Summary"
    >
      <b-card-text>
        As of
        <strong>{{ summary.updated ? formatDate(summary.updated) : 0 }}</strong>,
        <strong class="cases">{{ summary.cases ? summary.cases : 0 }}</strong> total cases have been reported,
        <strong
          class="dead"
        >{{ summary.deaths ? summary.deaths : 0 }}</strong> patients have died and
        <strong
          class="recover"
        >{{ summary.recovered ? summary.recovered : 0 }}</strong> have recovered.
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
            <span class="country-num cases">{{ country.cases ? country.cases : 0 }}</span> total cases reported
          </b-card-text>
          <b-card-text>
            <span class="country-num dead">{{ country.deaths ? country.deaths : 0 }}</span> total dead
          </b-card-text>
          <b-card-text>
            <span class="country-num recover">{{ country.recovered ? country.recovered : 0 }}</span> patients have recovered
          </b-card-text>
          <b-card-text>
            <span class="country-num critical">{{ country.critical ? country.critical : 0 }}</span> are in critial condition
          </b-card-text>
          <hr>
          <b-card-text>
            <span class="country-num new">{{ country.todayCases ? country.todayCases : 0 }}</span> new cases today
          </b-card-text>

          <b-card-text>
            <span class="country-num dead">{{ country.todayDeaths ? country.todayDeaths : 0 }}</span> have died today
          </b-card-text>
          <hr>
          <b-card-text>
            <span
              class="country-num"
            >{{ country.casesPerOneMillion ? country.casesPerOneMillion : 0 }}</span> cases per one million
          </b-card-text>
          <b-card-text>
            <span
              class="country-num death-per"
            >{{ country.deathsPerOneMillion ? country.deathsPerOneMillion : 0 }}</span> deaths per one million
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
export default {
  name: "Home",
  data: () => {
    return {
      countries: [],
      summary: {},
      countryName: "",
      error: "",
      loading: false
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
        .get("https://corona.lmao.ninja/countries?sort=country")
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
        .get("https://corona.lmao.ninja/all")
        .then(response => {
          this.summary = response.data;
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
  margin-bottom: 6px !important;
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
</style>