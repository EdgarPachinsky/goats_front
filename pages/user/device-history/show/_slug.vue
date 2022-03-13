<template>
  <div>
    <b-button
      type="button"
      variant="danger"
      to="/user/cabinet">Back to cabinet
    </b-button>

    <b-button
      type="button"
      variant="danger"
      to="/user/my-device/list">My Devices</b-button>

    <b-modal id="modal-1" size="md" title="History for animal">
      <template v-if="currentAnimalIdForGraphic">
        {{currentAnimalIdForGraphic}} ({{graphicTimeStart}} - {{graphicTimeEnd}})
        <hr>

        <BarChart
          :data="barChartData"
          :options="barChartOptions"
        />

      </template>
      <template v-else>
        Please select animal id
      </template>
    </b-modal>


    <div class="large-modal" v-if="showLgModal">

      <b-button
        class="closeLgModalButton"
        type="button"
        variant="danger"
        @click="openGraphic(true)"
      >
        Close
      </b-button>

      <b-row>
        <b-col sm="12">
          {{currentAnimalIdForGraphic}} ({{graphicTimeStart}} - {{graphicTimeEnd}})
        </b-col>
        <b-col sm="12">
          <div class="char-container">
            <BarChart :data="barChartData"
                      :options="barChartOptions"
            />
          </div>
        </b-col>
      </b-row>

    </div>

    <hr>
    <b-row>

      <b-col sm="12" md="3" lg="3">
        <b-form-group
          id="input-group-4" label="Custom Id:" label-for="currentIdGraphic">
        <b-form-input
          v-model="currentAnimalIdForGraphic"
          id="currentIdGraphic"
          placeholder="Animal ID"
          type="text"
        ></b-form-input>
        </b-form-group>
      </b-col>

      <b-col sm="12" md="3" lg="3">

        <b-form-group
          id="input-group-4" label="Graphic Time Start:" label-for="graphicTimeStart">
        <b-form-input
          v-model="graphicTimeStart"
          id="graphicTimeStart"
          placeholder="Date Start"
          type="date"
        ></b-form-input>
        </b-form-group>
      </b-col>


      <b-col sm="12" md="3" lg="3">
        <b-form-group
          id="input-group-4" label="Graphic Time End:" label-for="graphicTimeEnd">
        <b-form-input
          v-model="graphicTimeEnd"
          id="animalId"
          placeholder="Date End"
          type="date"
        ></b-form-input>
        </b-form-group>
      </b-col>

      <b-col sm="12" md="3" lg="3">
        <b-form-group
            id="input-group-4" label="_" label-for="graphicGet">
          <b-button class="margin-top-0 w-100" id="graphicGet" @click="openGraphic(false)">Get Graphic</b-button>
        </b-form-group>
      </b-col>
    </b-row>

    <hr>
    <b-row>
      <b-col sm="12" md="4" lg="4">
        <b-form-input
          v-model="currentIdForCustomId"
          id="animalId"
          placeholder="Current ID"
          type="text"
        ></b-form-input>
      </b-col>
      <b-col sm="12" md="4" lg="4">
        <b-form-input
          v-model="currentValueForCustomId"
          id="animalId"
          placeholder="Custom ID"
          type="text"
        ></b-form-input>
      </b-col>
      <b-col sm="12" md="4" lg="4">
        <b-button
          class="w-100"
          @click="saveCustomId()"
        >
          Save Custom Id
        </b-button>
      </b-col>
    </b-row>

    <hr>
    <b-row>
      <b-col sm="12" md="12" lg="12">
        <div class="main-t-body">
          <b-table
            bordered
            striped
            hover
            :items="deviceHistory"
            :fields="fields"
            :busy="loadedHistory"
            fixed
            :per-page="perPage"
            :current-page="currentPage"
          >
            <template #table-busy>
              <div class="text-center text-danger my-2">
                <b-spinner class="align-middle"></b-spinner>
                <strong>Loading...</strong>
              </div>
            </template>

          </b-table>

          <b-pagination
            v-model="currentPage"
            :total-rows="deviceHistory.length"
            :per-page="perPage"
            aria-controls="my-table"
          ></b-pagination>
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import {mapActions, mapState} from "vuex";
import BarChart from "../../../../components/BarChart";
import moment from "moment";

export default {
  async asyncData({params}) {
    const slug = params.slug // When calling /abc the slug will be "abc"
    return {slug}
  },

  components: { BarChart },

  computed: {
    ...mapState('device', ['deviceHistory', 'loadedHistory', 'weightsHistory', 'weightsMax'])
  },

  data() {
    return {

      showLgModal: false,
      barChartData: {
        labels: [],
        datasets: [
          {
            label: "Weight",
            data: [],
            backgroundColor: "rgba(20, 255, 0, 0.3)",
            borderColor: "rgba(100, 255, 0, 1)",
            borderWidth: 1,
          },
        ],
      },
      barChartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        legend: {
          display: false,
        },
        title: {
          display: true,
          text: "History chart",
          fontSize: 10,
          fontColor: "#6b7280",
        },
        tooltips: {
          backgroundColor: "#17BF62",
        },
        scales: {
          xAxes: [
            {
              gridLines: {
                display: true,
              },
            },
          ],
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
                max: this.weightsMax,
                min: 0,
                stepSize: 1,
              },
              gridLines: {
                display: false,
              },
            },
          ],
        },
      },

      currentIdForCustomId:null,
      currentValueForCustomId:null,

      fields: [
        {
          key: '#', label: '#',
          thStyle: {
            backgroundColor: '#3eef33',
            width: '60px',
            textAlign: 'center'
          },
        },
        {key: 'customId', label: 'Custom ID'},
        {key: 'animalId', label: 'Animal ID'},
        'weight',
        {key: 'createdAt', label: 'Created at'},
        {key: 'updatedAt', label: 'Updated at'}
      ],

      currentAnimalIdForGraphic:null,
      graphicTimeStart:null,
      graphicTimeEnd:null,
      perPage: 10,
      currentPage: 1,
    }
  },

  methods: {
    ...mapActions('device', ['loadDeviceHistory', 'loadDeviceHistory_','saveDeviceCustomId', 'dropHistory']),

    enumerateDaysBetweenDates(startDate, endDate) {
      var dates = [];
      dates.push(moment(startDate).format('YYYY-MM-DD'))

      var currDate = moment(startDate).startOf('day');
      var lastDate = moment(endDate).startOf('day');

      while(currDate.add(1, 'days').diff(lastDate) < 0) {
        console.log(currDate.toDate());
        dates.push(moment(currDate.clone().toDate()).format('YYYY-MM-DD'));
      }

      dates.push(moment(endDate).format('YYYY-MM-DD'))
      return dates;
    },

    saveHistoryCustomId(id){

      this.currentIdForCustomId = id
    },

    async saveCustomId(){

      await this.saveDeviceCustomId({
        animalId: this.currentIdForCustomId,
        customId: this.currentValueForCustomId,
      })

      this.currentIdForCustomId = null
      this.currentValueForCustomId = null
      await this.loadDeviceHistory({id:this.slug})
    },

    async openGraphic(isClose){
      if(isClose) {
        this.barChartData.labels = [];
        this.barChartData.datasets[0].data = [];
        this.showLgModal = !this.showLgModal;
        return
      }

      const dates = this.enumerateDaysBetweenDates(this.graphicTimeStart, this.graphicTimeEnd);

      await this.loadDeviceHistory_({
        id: this.slug,
        start: dates[0],
        end: dates[dates.length - 1],
        historyForOneAnimal:true,
        animalId: this.currentAnimalIdForGraphic
      })

      this.barChartData.labels = dates;
      this.barChartData.datasets[0].data = this.weightsHistory;
      this.showLgModal = !this.showLgModal;
    },

  },

  async mounted() {
    $nuxt.$emit('setPageTitle', {title: `History for device (${this.slug})`})

    await this.loadDeviceHistory({id:this.slug})
  }
}
</script>
