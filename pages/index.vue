<template>
  <v-container
    id="dashboard"
    fluid
    tag="section"
  >
    <v-row class="px-4">
      <v-col
        cols="12"
        sm="6"
        lg="3"
        class="my-10"
      >
        <base-material-stats-card
          color="info"
          icon="mdi-twitter"
          title="Followers"
          value="+245"
          sub-icon="mdi-clock"
          sub-text="Just Updated"
        />
      </v-col>

      <v-col
        cols="12"
        sm="6"
        lg="3"
        class="my-10"
      >
        <base-material-stats-card
          color="primary"
          icon="mdi-poll"
          title="Website Visits"
          value="75.521"
          sub-icon="mdi-tag"
          sub-text="Tracked from Google Analytics"
        />
      </v-col>

      <v-col
        cols="12"
        sm="6"
        lg="3"
        class="my-10"
      >
        <base-material-stats-card
          color="success"
          icon="mdi-store"
          title="Revenue"
          value="$ 34,245"
          sub-icon="mdi-calendar"
          sub-text="Last 24 Hours"
        />
      </v-col>

      <v-col
        cols="12"
        sm="6"
        lg="3"
        class="my-10"
      >
        <base-material-stats-card
          color="orange"
          icon="mdi-sofa"
          title="Bookings"
          value="184"
          sub-icon="mdi-alert"
          sub-icon-color="red"
          sub-text="Get More Space..."
        />
      </v-col>

      <v-col
        cols="12"
        md="6"
        class="my-11"
      >
        <base-material-card
          color="warning"
          class="px-5 py-3"
        >
          <template v-slot:heading>
            <div class="display-2 font-weight-light">
              Clinic Stats
            </div>

            <div class="subtitle-1 font-weight-light">
              Available clinics in your region
            </div>
          </template>
          <v-card-text>
            <v-data-table
              :headers="headers"
              :items="clinicNames"
              sort-by="name"
            >
              <template v-slot:top>
                <v-toolbar flat color="white">
                  <!-- <v-toolbar-title>My CRUD</v-toolbar-title>
                  <v-divider
                    class="mx-4"
                    inset
                    vertical
                  /> -->
                  <v-spacer />
                  <v-btn
                    color="success"
                    dark
                    class="mb-2 mr-1"
                    @click="updateClinics"
                  >
                    Save
                  </v-btn>

                  <v-dialog v-model="dialog" max-width="500px" @keydown.enter="save">
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn
                        color="primary"
                        dark
                        class="mb-2"
                        v-bind="attrs"
                        v-on="on"
                      >
                        New Clinic
                      </v-btn>
                    </template>
                    <v-card>
                      <v-card-title>
                        <span class="headline">{{ formTitle }}</span>
                      </v-card-title>

                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12" sm="6" md="4">
                              <v-text-field v-model="editedItem.name" autofocus label="Name" />
                            </v-col>
                          </v-row>
                        </v-container>
                      </v-card-text>

                      <v-card-actions>
                        <v-spacer />
                        <v-btn color="blue darken-1" text @click="close">
                          Cancel
                        </v-btn>
                        <v-btn color="blue darken-1" text @click="save">
                          Done
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-toolbar>
              </template>

              <template v-slot:[`item.actions`]="{ item }">
                <v-icon
                  small
                  class="mr-2"
                  @click="editItem(item)"
                >
                  mdi-pencil
                </v-icon>
                <v-icon
                  small
                  @click="deleteItem(item)"
                >
                  mdi-delete
                </v-icon>
              </template>
            </v-data-table>
          </v-card-text>
        </base-material-card>
      </v-col>

      <v-col
        cols="12"
        md="6"
        class="my-11"
      >
        <base-material-card class="px-5 py-3">
          <template v-slot:heading>
            <v-tabs
              v-model="tabs"
              background-color="transparent"
              slider-color="white"
            >
              <span
                class="subheading font-weight-light mx-3"
                style="align-self: center"
              >Tasks:</span>
              <v-tab class="mr-3">
                <v-icon class="mr-2">
                  mdi-bug
                </v-icon>
                Bugs
              </v-tab>
              <v-tab class="mr-3">
                <v-icon class="mr-2">
                  mdi-code-tags
                </v-icon>
                Website
              </v-tab>
              <v-tab>
                <v-icon class="mr-2">
                  mdi-cloud
                </v-icon>
                Server
              </v-tab>
            </v-tabs>
          </template>

          <v-tabs-items
            v-model="tabs"
            class="transparent"
          >
            <v-tab-item
              v-for="n in 3"
              :key="n"
            >
              <v-card-text>
                <v-data-table
                  :headers="headers"
                  :items="clinicNames"
                  sort-by="name"
                >
                  <template v-slot:top>
                    <v-toolbar flat color="white">
                      <!-- <v-toolbar-title>My CRUD</v-toolbar-title>
                      <v-divider
                        class="mx-4"
                        inset
                        vertical
                      /> -->
                      <v-spacer />
                      <v-btn
                        color="success"
                        dark
                        class="mb-2 mr-1"
                        @click="updateClinics"
                      >
                        Save
                      </v-btn>

                      <v-dialog v-model="dialog" max-width="500px" @keydown.enter="save">
                        <template v-slot:activator="{ on, attrs }">
                          <v-btn
                            color="primary"
                            dark
                            class="mb-2"
                            v-bind="attrs"
                            v-on="on"
                          >
                            New Clinic
                          </v-btn>
                        </template>
                        <v-card>
                          <v-card-title>
                            <span class="headline">{{ formTitle }}</span>
                          </v-card-title>

                          <v-card-text>
                            <v-container>
                              <v-row>
                                <v-col cols="12" sm="6" md="4">
                                  <v-text-field v-model="editedItem.name" autofocus label="Name" />
                                </v-col>
                              </v-row>
                            </v-container>
                          </v-card-text>

                          <v-card-actions>
                            <v-spacer />
                            <v-btn color="blue darken-1" text @click="close">
                              Cancel
                            </v-btn>
                            <v-btn color="blue darken-1" text @click="save">
                              Done
                            </v-btn>
                          </v-card-actions>
                        </v-card>
                      </v-dialog>
                    </v-toolbar>
                  </template>

                  <template v-slot:[`item.actions`]="{ item }">
                    <v-icon
                      small
                      class="mr-2"
                      @click="editItem(item)"
                    >
                      mdi-pencil
                    </v-icon>
                    <v-icon
                      small
                      @click="deleteItem(item)"
                    >
                      mdi-delete
                    </v-icon>
                  </template>
                </v-data-table>

                <!-- <template v-for="(task, i) in tasks[tabs]">
                  <v-row
                    :key="i"
                    align="center"
                  >
                    <v-col cols="1">
                      <v-list-item-action>
                        <v-checkbox
                          v-model="task.value"
                          color="secondary"
                        />
                      </v-list-item-action>
                    </v-col>

                    <v-col cols="9">
                      <div
                        class="font-weight-light"
                        v-text="task.text"
                      />
                    </v-col>

                    <v-col
                      cols="2"
                      class="text-right"
                    >
                      <v-icon class="mx-1">
                        mdi-pencil
                      </v-icon>
                      <v-icon
                        color="error"
                        class="mx-1"
                      >
                        mdi-close
                      </v-icon>
                    </v-col>
                  </v-row>
                </template> -->
              </v-card-text>
            </v-tab-item>
          </v-tabs-items>
        </base-material-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// import MaterialChartCard from '@/components/base/MaterialChartCard'
import MaterialCard from '@/components/base/MaterialCard'
import MaterialStatsCard from '@/components/base/MaterialStatsCard'

export default {
  // name: 'DashboardDashboard',

  components: {
    // BaseMaterialChartCard: MaterialChartCard,
    BaseMaterialCard: MaterialCard,
    BaseMaterialStatsCard: MaterialStatsCard
  },

  async asyncData (context) {
    const { data } = await context.$axios.get('http://localhost:8080/pkdadm')
    const clinicNames = data.clinicNames.map((val, idx) => {
      return {
        id: data.clinicIds[idx],
        name: val
      }
    })

    return {
      jknName: data.jknName,
      pkdName: data.pkdName,
      pkdAdmId: data.pkdAdmId,
      clinicNames
    }
  },

  data () {
    return {
      dailySalesChart: {
        data: {
          labels: ['M', 'T', 'W', 'T', 'F', 'S', 'S'],
          series: [
            [12, 17, 7, 17, 23, 18, 38]
          ]
        },
        options: {
          lineSmooth: this.$chartist.Interpolation.cardinal({
            tension: 0
          }),
          low: 0,
          high: 50, // creative tim: we recommend you to set the high sa the biggest value + something for a better look
          chartPadding: {
            top: 0,
            right: 0,
            bottom: 0,
            left: 0
          }
        }
      },
      dataCompletedTasksChart: {
        data: {
          labels: ['12am', '3pm', '6pm', '9pm', '12pm', '3am', '6am', '9am'],
          series: [
            [230, 750, 450, 300, 280, 240, 200, 190]
          ]
        },
        options: {
          lineSmooth: this.$chartist.Interpolation.cardinal({
            tension: 0
          }),
          low: 0,
          high: 1000, // creative tim: we recommend you to set the high sa the biggest value + something for a better look
          chartPadding: {
            top: 0,
            right: 0,
            bottom: 0,
            left: 0
          }
        }
      },
      emailsSubscriptionChart: {
        data: {
          labels: ['Ja', 'Fe', 'Ma', 'Ap', 'Mai', 'Ju', 'Jul', 'Au', 'Se', 'Oc', 'No', 'De'],
          series: [
            [542, 443, 320, 780, 553, 453, 326, 434, 568, 610, 756, 895]

          ]
        },
        options: {
          axisX: {
            showGrid: false
          },
          low: 0,
          high: 1000,
          chartPadding: {
            top: 0,
            right: 5,
            bottom: 0,
            left: 0
          }
        },
        responsiveOptions: [
          ['screen and (max-width: 640px)', {
            seriesBarDistance: 5,
            axisX: {
              labelInterpolationFnc (value) {
                return value[0]
              }
            }
          }]
        ]
      },
      headers: [
        // {
        //   sortable: false,
        //   text: 'ID',
        //   value: 'id'
        // },
        {
          sortable: true,
          text: 'Name',
          value: 'name'
        },
        {
          sortable: false,
          text: 'Actions',
          value: 'actions'
        }
      ],
      dialog: false,
      editedIndex: -1,
      editedItem: {
        name: ''
      },
      defaultItem: {
        name: ''
      },
      newClinicNames: [],
      newClinicNamesIdx: -1,
      editedClinicNames: [],
      editedClinicNamesIdx: -1,
      delClinicNames: [],
      delClinicNamesIdx: -1,
      isNewClinic: -1,
      items: [
        {
          id: 1,
          name: 'Dakota Rice'
        },
        {
          id: 2,
          name: 'Minerva Hooper'
        },
        {
          id: 3,
          name: 'Sage Rodriguez'
        },
        {
          id: 4,
          name: 'Philip Chanley'
        },
        {
          id: 5,
          name: 'Doris Greene'
        }
      ],
      tabs: 0,
      tasks: {
        0: [
          {
            text: 'Sign contract for "What are conference organizers afraid of?"',
            value: true
          },
          {
            text: 'Lines From Great Russian Literature? Or E-mails From My Boss?',
            value: false
          },
          {
            text: 'Flooded: One year later, assessing what was lost and what was found when a ravaging rain swept through metro Detroit',
            value: false
          },
          {
            text: 'Create 4 Invisible User Experiences you Never Knew About',
            value: true
          }
        ],
        1: [
          {
            text: 'Flooded: One year later, assessing what was lost and what was found when a ravaging rain swept through metro Detroit',
            value: true
          },
          {
            text: 'Sign contract for "What are conference organizers afraid of?"',
            value: false
          }
        ],
        2: [
          {
            text: 'Lines From Great Russian Literature? Or E-mails From My Boss?',
            value: false
          },
          {
            text: 'Flooded: One year later, assessing what was lost and what was found when a ravaging rain swept through metro Detroit',
            value: true
          },
          {
            text: 'Sign contract for "What are conference organizers afraid of?"',
            value: true
          }
        ]
      },
      list: {
        0: false,
        1: false,
        2: false
      }
    }
  },

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    }
  },

  methods: {
    complete (index) {
      this.list[index] = !this.list[index]
    },

    updateClinics () {
      const output = {}

      output.jknName = this.jknName
      output.pkdName = this.pkdName
      output.pkdAdmId = this.pkdAdmId
      output.addMetadatas = this.newClinicNames
      output.editMetadatas = this.editedClinicNames
      output.delMetadatas = this.delClinicNames

      const data = JSON.stringify(output)
      // eslint-disable-next-line
      console.log(output)
      // eslint-disable-next-line
      console.log(data)
      this.$axios.post('http://localhost:8081/updateclinicbypkdadm', data)
        .then((response) => {
          alert('saveClinicDataToDirectory done successfully!')
          this.newClinicNames = []
          this.editedClinicNames = []
          this.delClinicNames = []
        }, (response) => {
          alert('saveClinicDataToDirectory failed!')
        })
    },

    editItem (item) {
      this.editedIndex = this.clinicNames.indexOf(item)
      // this.editedClinicNamesIdx = this.editedClinicNames.indexOf(item)
      this.editedClinicNamesIdx = this.editedClinicNames.findIndex(
        i => i.name === item.name)
      this.isNewClinic = this.newClinicNames.findIndex(
        i => i.name === item.name)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem (item) {
      const index = this.clinicNames.indexOf(item)

      if (confirm('Are you sure you want to remove this clinic?')) {
        this.clinicNames.splice(index, 1)

        const editedClinicIdx = this.editedClinicNames.findIndex(
          i => i.name === item.name)
        if (editedClinicIdx > -1) {
          this.editedClinicNames.splice(editedClinicIdx, 1)
        }

        const newClinicIdx = this.newClinicNames.findIndex(
          i => i.name === item.name)
        if (newClinicIdx > -1) {
          // Remove newly added clinic in memory
          this.newClinicNames.splice(newClinicIdx, 1)
        } else {
          // Remove existing clinic in directory
          this.delClinicNames.push(item)
        }
      }
    },

    close () {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save () {
      if (this.editedIndex > -1) {
        if (this.clinicNames[this.editedIndex].name === this.editedItem.name) {
          this.close()
          return
        }
        Object.assign(this.clinicNames[this.editedIndex], this.editedItem)
        if (this.editedClinicNamesIdx > -1) {
          // The item has been edited before
          Object.assign(this.editedClinicNames[this.editedClinicNamesIdx], this.editedItem)
        } else if (!(this.isNewClinic > -1)) {
          // The item is edited for the first time
          this.editedClinicNames.push(this.editedItem)
        }
      } else if (this.editedItem.name === '') {
        // Do nothing
      } else {
        this.clinicNames.push(this.editedItem)
        this.newClinicNames.push(this.editedItem)
      }
      this.close()
    }
  }
}
</script>
