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

    <b-button
      type="button"
      variant="danger"
      @click="reloadPage()">Reload Page</b-button>

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

      <b-button
        class="exportButton"
        type="button"
        variant="danger"
        @click="exportCSV()"
      >
        Export CSV
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

        <b-row>
          <b-col sm="6" md="6" lg="6">
            <b-form-group
              id="input-group-4" label="_" label-for="graphicGet">
              <b-button class="margin-top-0 w-100" id="graphicGet" @click="openGraphic(false)">
                Graph <b-icon icon="graph-up" aria-hidden="true"></b-icon>
              </b-button>
            </b-form-group>
          </b-col>

          <b-col sm="6" md="6" lg="6">
            <b-form-group
              id="input-group-4" label="_" label-for="graphicGet">
              <b-button
                variant="outline-danger"
                class="margin-top-0 w-100" id="graphicGet" @click="deleteHistoryElement(false, null, null)">
                DELETE <b-icon icon="trash" aria-hidden="true"></b-icon>
              </b-button>
            </b-form-group>
          </b-col>
        </b-row>
      </b-col>
    </b-row>

    <hr>
    <b-row>
      <b-col sm="12" md="4" lg="4">
        <b-form-input
          v-model="currentIdForCustomId"
          id="animalId"
          placeholder="Current ID"
          type="number"
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

            <template
              #cell(actions)="data">
              <b-button
                type="button"
                variant="outline-danger"
                class="margin-bottom-15"
                size="sm"
                :disabled="data.item._id===$auth.user._id"
                @click="deleteHistoryElement(true, data.item._id, data.item.customId)"
              >
                DELETE <b-icon icon="trash" aria-hidden="true"></b-icon>
              </b-button>

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
    const slug = params.slug
    return {slug}
  },

  components: { BarChart },

  computed: {
    ...mapState('device', ['deviceHistory', 'loadedHistory', 'weightsHistory', 'weightsMax'])
  },

  data() {
    return {
      toastCount: 0,
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
        {key: 'updatedAt', label: 'Updated at'},
        {key: 'actions', label: 'Actions'},
      ],

      currentAnimalIdForGraphic:null,
      graphicTimeStart:null,
      graphicTimeEnd:null,
      perPage: 10,
      currentPage: 1,
    }
  },

  methods: {
    ...mapActions('device',
      [
        'loadDeviceHistory',
        'loadDeviceHistory_',
        'saveDeviceCustomId',
        'dropHistory',
        'deleteHistoryElement_'
      ]
    ),

    reloadPage(){
      window.location.reload(true);
    },

    makeToast(append = false, message) {
      this.toastCount++
      this.$bvToast.toast(message, {
        title: 'Action Done',
        autoHideDelay: 4000,
        appendToast: append
      })
    },

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

      if(
        !this.currentIdForCustomId ||
        !this.currentValueForCustomId
      ){
        this.makeToast(false, `Current ID and custom ID must be specified!`)
        return
      }

      this.saveDeviceCustomId({
        animalId: this.currentIdForCustomId,
        customId: this.currentValueForCustomId
      }).then(async (data) => {
        this.makeToast(false, `Updated ${data.nModified} records,
        Current ID[${this.currentIdForCustomId}] -> Custom ID[${this.currentValueForCustomId}]!`)

        this.currentIdForCustomId = null
        this.currentValueForCustomId = null
        await this.loadDeviceHistory({id:this.slug})
      })
    },

    async deleteHistoryElement(singleRecord, elementId, customId){
      if(singleRecord){

        this.deleteHistoryElement_({
          _id: elementId,
          many: false
        }).then(async (data) => {

          let message = `Deleted history record for ${customId}(${elementId})`;
          this.makeToast(false, message)

          await this.loadDeviceHistory({id:this.slug})
        })
      }else{

        if(
          !this.graphicTimeStart ||
          !this.graphicTimeEnd
        ){
          this.makeToast(false, `Start and end dates must be specified!`)
          return
        }

        this.deleteHistoryElement_({
          customId: this.currentAnimalIdForGraphic,
          createdAt: {
            $gte: this.graphicTimeStart,
            $lte: this.graphicTimeEnd
          },
          many: true
        }).then(async (data) => {

          let message = `Deleted history from ${this.graphicTimeStart} to ${this.graphicTimeEnd}`;
          if(this.currentAnimalIdForGraphic){
            message += `, for ID ${this.currentAnimalIdForGraphic}`
          }
          if(data.deletedCount){
            message += `, deleted count ${data.deletedCount}`
          }

          this.makeToast(false, message)
          await this.loadDeviceHistory({id:this.slug})
        })
      }
    },

    async openGraphic(isClose){
      if(isClose) {
        this.showLgModal = !this.showLgModal;
        return
      }

      if(
        !this.currentAnimalIdForGraphic ||
        !this.graphicTimeStart ||
        !this.graphicTimeEnd
      ){
        this.makeToast(false, `Animal ID, start date and end date must be specified!`)
      }


      const dates = this.enumerateDaysBetweenDates(this.graphicTimeStart, this.graphicTimeEnd);

      this.loadDeviceHistory_({
        id: this.slug,
        start: dates[0],
        end: dates[dates.length - 1],
        historyForOneAnimal:true,
        animalId: this.currentAnimalIdForGraphic
      }).then(() => {

        this.barChartData.labels = dates;
        this.barChartData.datasets[0].data = this.weightsHistory;
        this.showLgModal = !this.showLgModal;
      })
    },

    async exportCSV(){

      let csv = 'Date,Weight\n';
      for (let i = 0; i < this.barChartData.labels.length; i++) {
        csv += `${this.barChartData.labels[i]}, ${this.barChartData.datasets[0].data[i] || `-`}`
        csv += "\n";
      }

      let extension = `csv`
      const anchor = document.createElement('a');
      anchor.href = 'data:text/csv;charset=utf-8,' + encodeURIComponent(csv);
      anchor.target = '_blank';

      let downloadUrl = `Weight_export_${this.currentAnimalIdForGraphic}`;
      if(this.graphicTimeStart){
        downloadUrl += `_${this.graphicTimeStart}`
      }
      if(this.graphicTimeEnd){
        downloadUrl += `_${this.graphicTimeEnd}`;
      }

      downloadUrl += `.${extension}`;

      anchor.download = downloadUrl;
      anchor.click();
    }
  },

  async mounted() {
    $nuxt.$emit('setPageTitle', {title: `History for device (${this.slug})`})

    await this.loadDeviceHistory({id:this.slug})
  }
}
</script>
