<template>
  <div id="app">
    <b-container>
      <b-row>
        <h1 class="mx-auto">Athletics Results Visualization</h1>
      </b-row>
      <b-row>
        <p class="mx-auto">Visualizing Athletics Results in Hong Kong Secondary School Competition</p>
      </b-row>
      <hr />
      <b-row>
        <b-col cols="2">
          <b-form-group label-cols="4" label="School">
            <b-form-select
              @change="changeSchool();getNames()"
              v-model="selectedSchool"
              :options="schoolOptions"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group label-cols="4" label="Sex">
            <b-form-select @change="changeSex();getNames()" v-model="selectedSex" :options="sexOptions"></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="3">
          <b-form-group label-cols="4" label="Event">
            <b-form-select @change="getNames()" v-model="selectedEvent" :options="eventOptions"></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="4">
          <b-form-group label-cols="4" label="Name">
            <b-form-select @change="changeName()" v-model="selectedName" :options="nameOptions"></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="1">
          <b-button>Query</b-button>
        </b-col>
      </b-row>
      <hr />
      <b-row>
        <b-col>
          <chart :chartData="chartdata" :options="chartOptions"></chart>
        </b-col>
        <b-col>
          <b-table striped hover :items="items" :fields="fields"></b-table>
        </b-col>
      </b-row>
      <hr />
      <b-row>
        <p class="mx-auto">
          Data source from
          <a href="http://hkssf-hk.org.hk">HKSSF</a>
        </p>
      </b-row>
      <b-row>
        <p class="mx-auto">
          Designed by
          <a href="http://cameronlai.com">Cameron Lai</a>
        </p>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import json from "./assets/data.json";
import Chart from "./components/Chart.vue";

export default {
  name: "App",
  components: {
    Chart
  },
  data: function() {
    return {
      chartOptions: {
        scales: {
          yAxes: [
            {
              ticks: {
                display: true
              },
              gridLines: {
                display: true
              }
            }
          ],
          xAxes: [
            {
              gridLines: {
                display: true
              }
            }
          ]
        },
        legend: {
          display: true
        },
        responsive: true,
        maintainAspectRatio: false
      },
      chartData: {},
      fields: [
        {
          key: "0",
          label: "Name"
        },
        {
          key: "1",
          label: "School"
        },
        {
          key: "2",
          label: "Event"
        },
        {
          key: "3",
          label: "Sex"
        },
        {
          key: "4",
          label: "Result"
        },
        {
          key: "5",
          label: "Date"
        }
      ],
      items: [],
      sexOptions: [],
      schoolOptions: [],
      eventOptions: [],
      nameOptions: [],
      selectedSex: null,
      selectedSchool: null,
      selectedEvent: null,
      selectedName: null
    };
  },
  created: function() {
    this.schoolOptions = [...new Set(json.map(item => item["1"]))].sort();
    console.log(this.schoolOptions);
    this.selectedSchool = this.schoolOptions[0];
    console.log(this.selectedSchool);
    this.changeSchool();
    this.getNames();
  },
  methods: {
    changeSchool: function() {
      let items = json.filter(item => {
        const ret = item["1"] == this.selectedSchool;
        return ret;
      });
      this.sexOptions = [...new Set(items.map(item => item["3"]))].reverse();
      console.log(this.sexOptions);
      this.selectedSex = this.sexOptions[0];
      console.log(this.selectedSex);
      this.changeSex();    
    },
    changeSex: function() {
      let items = json.filter(item => {
        const ret =
          item["3"] == this.selectedSex && item["1"] == this.selectedSchool;
        return ret;
      });
      this.eventOptions = [...new Set(items.map(item => item["2"]))].sort();
      console.log(this.eventOptions);
      this.selectedEvent = this.eventOptions[0];
      console.log(this.selectedEvent);
    },    
    getNames: function() {
      const items = json.filter(item => {
        const ret =
          item["1"] == this.selectedSchool &&
          item["2"] == this.selectedEvent &&
          item["3"] == this.selectedSex;
        return ret;
      });
      this.nameOptions = [...new Set(items.map(item => item["0"]))].sort();
      this.selectedName = this.nameOptions[0];
      this.changeName();
    },
    changeName: function() {
      this.items = json.filter(item => {
        const ret =
          item["0"] == this.selectedName &&
          item["1"] == this.selectedSchool &&
          item["2"] == this.selectedEvent &&
          item["3"] == this.selectedSex;
        return ret;
      });
      console.log(this.items);
      this.updateChart();
    },
    updateChart: function() {
      console.log("Update Chart");
      const dates = this.items.map(item => item["5"]);
      const results = this.items.map(item => item["4"]);

      console.log(dates);
      console.log(results);

      this.chartdata = {
        labels: dates,
        datasets: [
          {
            label: this.items[0]["0"], // Name,
            backgroundColor: "#ADD8E6",
            data: results
          }
        ]
      };
      console.log(this.chartdata);
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
