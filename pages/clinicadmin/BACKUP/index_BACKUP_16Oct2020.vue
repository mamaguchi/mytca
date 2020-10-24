<template>
  <v-container
    id="user-profile"
    fluid
    tag="section"
  >
    <ValidationObserver ref="clinicObserver" v-slot="{ dirty, invalid }">
      <base-material-card
        color="info"
        max-width="640"
        rounded
        class="v-card--material-chart mt-10 mx-auto rounded-lg"
        v-bind="$attrs"
        v-on="$listeners"
      >
        <template v-slot:heading>
          <div class="display-2 font-weight-light">
            <v-icon
              large
              left
            >
              mdi-hospital-building
            </v-icon>
            Clinic Overview
          </div>
        </template>

        <v-card-text class="font-weight-medium grey--text text--darken-4">
          <v-row no-gutters class="mx-auto">
            <v-col cols="10" class="text-left mt-4 ml-5">
              <ValidationProvider v-slot="{ errors }" rules="required|min:1">
                <v-text-field
                  v-model="clinicName"
                  label="Clinic Name"
                  :error-messages="errors"
                  clear-icon="mdi-close-circle-outline"
                  disabled
                />
              </ValidationProvider>
            </v-col>

            <v-col cols="5" class="text-left ml-5">
              <ValidationProvider v-slot="{ errors }" rules="required|min:1">
                <v-text-field
                  v-model="clinicState"
                  label="State"
                  :error-messages="errors"
                  clear-icon="mdi-close-circle-outline"
                />
              </ValidationProvider>
            </v-col>

            <v-col cols="5" class="text-left ml-5">
              <ValidationProvider v-slot="{ errors }" rules="required|min:1">
                <v-text-field
                  v-model="clinicDistrict"
                  label="District"
                  :error-messages="errors"
                  clear-icon="mdi-close-circle-outline"
                />
              </ValidationProvider>
            </v-col>
          </v-row>

          <!--  CLINIC-CLOSE-DAYS  -->
          <ValidationProvider>
            <v-row no-gutters class="mx-auto">
              <v-col cols="10" class="text-left ml-5 mt-2">
                <label>Clinic is close on</label>
                <v-treeview
                  v-model="clinicCloseDays"
                  dense
                  selectable
                  :items="daysOfWeek"
                  class="grey--text text--darken-1 ml-16 pl-16 mt-2"
                />
              </v-col>
            </v-row>
          </ValidationProvider>

          <!--  CLINIC-PUBLIC-HOLIDAY  -->
          <ValidationProvider>
            <v-row no-gutters class="mx-auto">
              <v-col cols="10" class="d-flex flex-column text-left ml-5 mt-2">
                <label>Public holiday</label>
                <v-date-picker
                  v-model="clinicPubHol"
                  class="mt-1 ml-12"
                  no-title
                  multiple
                  :show-current="false"
                  @click:date="dateClick"
                />
              </v-col>
            </v-row>
          </ValidationProvider>

          <!--  CLINIC-STAFF  -->
          <v-form
            v-model="addNewStaffInputValid"
          >
            <v-row
              class="pa-4"
              justify="space-between"
            >
              <v-col cols="5" class="d-flex flex-column">
                <label>Register new staff</label>

                <v-text-field
                  v-model="newStaffName"
                  class="mt-2"
                  label="Name"
                  outlined
                  flat
                  clearable
                  :rules="nameRules"
                  @click="newStaffStatusHint.msg=''"
                  @click:clear="newStaffStatusHint.msg=''"
                />

                <v-text-field
                  v-model="newStaffId"
                  class="mt-n10"
                  label="ID/IC number"
                  outlined
                  flat
                  clearable
                  :rules="idRules"
                  persistent-hint
                  :hint="newStaffStatusHint.msg"
                  @click="newStaffStatusHint.msg=''"
                  @click:clear="newStaffStatusHint.msg=''"
                />

                <!-- eslint-disable-next-line  -->
                    <v-spacer></v-spacer>

                <v-row justify="center" align="end">
                  <v-btn
                    :disabled="!addNewStaffInputValid"
                    class="font-weight-medium grey--text text--darken-2"
                    @click="addStaffToClinic(
                      newStaffStatusHint
                    )"
                  >
                    <v-icon left>
                      mdi-account-plus
                    </v-icon>
                    Register
                  </v-btn>
                </v-row>
              </v-col>

              <v-divider vertical />

              <!--  CLINIC-STAFF--CONTEXT-VIEW  -->
              <v-col>
                <label class="ml-4">Existing staff</label>

                <v-text-field
                  v-model="searchClinicStaffToDel"
                  class="mt-2 ml-4"
                  label="Filter"
                  outlined
                  flat
                  hide-details
                  clearable
                />

                <v-virtual-scroll
                  :items="clinicStaff"
                  :item-height="1000"
                  height="250"
                  class="ml-n2 mt-2"
                >
                  <template v-slot="{ item }">
                    <v-list-item>
                      <v-list-item-content>
                        <v-treeview
                          v-model="clinicStaffToDel"
                          :items="item"
                          activatable
                          :active.sync="activeClinicStaff"
                          :search="searchClinicStaffToDel"
                          open-all
                          dense
                          selectable
                          transition
                          class="grey--text text--darken-1"
                        >
                          <!-- eslint-disable-next-line  -->
                        <template v-slot:prepend="{ item }">
                            <v-icon v-if="!item.children">
                              mdi-account
                            </v-icon>
                          </template>
                        </v-treeview>
                      </v-list-item-content>
                    </v-list-item>
                  </template>
                </v-virtual-scroll>

                <v-row justify="center">
                  <v-btn
                    :disabled="clinicStaffToDel.length === 0"
                    class="font-weight-medium grey--text text--darken-2 mt-5"
                    @click="delStaffFrmClinic"
                  >
                    <v-icon left>
                      mdi-delete
                    </v-icon>
                    Delete
                  </v-btn>
                </v-row>
              </v-col>
            </v-row>
          </v-form>

          <!-- <v-row no-gutters>
            <v-col cols="20">
              <v-divider />
            </v-col>
          </v-row> -->
        </v-card-text>

        <template v-slot:actions>
          <v-row no-gutters justify="center">
            <v-btn
              :disabled="(!dirty &&
                !clinicHasAddedStaff.bool &&
                !clinicHasDeletedStaff.bool) ||
                invalid"
              text
              class="font-weight-medium grey--text text--darken-2 mt-n1 mb-n2"
              @click="saveClinicDataToDirectory(clinicName)"
            >
              <v-icon left>
                mdi-content-save
              </v-icon>
              Save
            </v-btn>
          </v-row>
        </template>
      </base-material-card>
    </ValidationObserver>

    <!-- ====================  -->
    <!-- SERVICE LIST SECTION  -->
    <!-- ====================  -->
    <v-row justify="center">
      <v-col cols="10">
        <keep-alive>
          <v-expansion-panels focusable class="mt-10" max-width="600">
            <v-expansion-panel v-for="service in clinicServices" :key="service.serviceName">
              <v-expansion-panel-header
                color="success"
                class="py-1"
              >
                <span class="title font-weight-regular">
                  <v-icon
                    left
                    color="white"
                  >
                    mdi-doctor
                  </v-icon>
                  <span class="white--text headline">
                    {{ service.serviceName.toUpperCase() }} Service
                  </span>
                </span>
              </v-expansion-panel-header>
              <v-expansion-panel-content>
                <ValidationObserver :ref="`serviceObserver_${service.serviceName}`" v-slot="{ dirty, invalid }">
                  <v-card
                    flat
                    rounded
                    class="rounded-lg"
                  >
                    <v-card-text class="font-weight-medium grey--text text--darken-4">
                      <v-row no-gutters class="mx-auto">
                        <v-col cols="5" class="text-left mt-4 ml-5">
                          <ValidationProvider v-slot="{ errors }" rules="required|numeric|min:1">
                            <v-text-field
                              v-model="service.avgConsultTime"
                              label="Average Consultation Time"
                              :error-messages="errors"
                              clear-icon="mdi-close-circle-outline"
                            />
                          </ValidationProvider>
                        </v-col>

                        <v-col cols="5" class="text-left mt-4 ml-10">
                          <ValidationProvider v-slot="{ errors }" rules="required|numeric|min:1">
                            <v-text-field
                              v-model="service.maxPtAtATime"
                              label="Maximum Patient At A Time"
                              :error-messages="errors"
                              clear-icon="mdi-close-circle-outline"
                            />
                          </ValidationProvider>
                        </v-col>
                      </v-row>

                      <v-row no-gutters class="mt-4">
                        <v-col cols="20">
                          <v-divider />
                        </v-col>
                      </v-row>

                      <!--  SERVICE-STAFF  -->
                      <v-row
                        class="pa-4"
                        justify="space-between"
                      >
                        <v-col cols="5" class="d-flex flex-column">
                          <label>Add new staff to {{ service.serviceName.toUpperCase() }} service</label>

                          <v-autocomplete
                            v-model="service.search.clinicServiceStaffToAdd"
                            return-object
                            class="mt-2"
                            :items="clinicStaff[0][0].children"
                            outlined
                            color="blue-grey lighten-2"
                            label="Search all clinic staff"
                            item-text="name"
                            item-value="id"
                            clearable
                            persistent-hint
                            :hint="service.hint.msg"
                            @change="service.hint.msg=''"
                          >
                            <template v-slot:item="data">
                              <v-list-item-content>
                                <v-list-item-title class="font-weight-medium" v-text="data.item.name" />
                              <!-- <v-list-item-subtitle class="caption" v-text="data.item.group" /> -->
                              </v-list-item-content>
                            </template>
                          </v-autocomplete>

                          <!-- eslint-disable-next-line  -->
                        <v-spacer></v-spacer>

                          <v-row justify="center" align="end">
                            <v-btn
                              :disabled="service.search.clinicServiceStaffToAdd===null ||
                                service.search.clinicServiceStaffToAdd===undefined"
                              class="font-weight-medium grey--text text--darken-2"
                              @click="addStaffToClinicService(
                                service.serviceName,
                                service.hint,
                                service.clinicServiceStaffIds
                              )"
                            >
                              <v-icon left>
                                mdi-account-plus
                              </v-icon>
                              Add
                            </v-btn>
                          </v-row>
                        </v-col>

                        <v-divider vertical />

                        <!--  SERVICE-STAFF--CONTEXT-VIEW  -->
                        <v-col>
                          <label class="ml-4">Existing staff</label>

                          <v-text-field
                            v-model="service.search.clinicServiceStaffToDel"
                            class="mt-2 ml-4"
                            label="Filter"
                            outlined
                            flat
                            hide-details
                            clearable
                          />

                          <v-virtual-scroll
                            :items="service.clinicServiceStaffIds"
                            :item-height="1000"
                            height="250"
                            class="ml-n2 mt-2"
                          >
                            <template v-slot="{ item }">
                              <v-list-item>
                                <v-list-item-content>
                                  <v-treeview
                                    v-model="service.clinicServiceStaffToDel"
                                    :items="item"
                                    :search="service.search.clinicServiceStaffToDel"
                                    open-all
                                    dense
                                    selectable
                                    activatable
                                    open-on-click
                                    :active.sync="service.activeClinicServiceStaff"
                                    transition
                                    class="grey--text text--darken-1"
                                  >
                                    <!-- eslint-disable-next-line  -->
                            <template v-slot:prepend="{ item }">
                                      <v-icon v-if="!item.children">
                                        mdi-account
                                      </v-icon>
                                    </template>
                                  </v-treeview>
                                </v-list-item-content>
                              </v-list-item>
                            </template>
                          </v-virtual-scroll>

                          <v-row justify="center">
                            <v-btn
                              :disabled="service.clinicServiceStaffToDel.length===0"
                              class="font-weight-medium grey--text text--darken-2 mt-5"
                              @click="delStaffFrmClinicService(
                                service.serviceName,
                                service.clinicServiceStaffIds
                              )"
                            >
                              <v-icon left>
                                mdi-delete
                              </v-icon>
                              Delete
                            </v-btn>
                          </v-row>
                        </v-col>
                      </v-row>

                      <v-row no-gutters>
                        <v-col cols="20">
                          <v-divider />
                        </v-col>
                      </v-row>

                      <!--  AVAILABLE-DAYS  -->
                      <v-row
                        class="pa-4"
                        justify="space-between"
                      >
                        <v-col cols="5">
                          <label>{{ service.serviceName.toUpperCase() }} service is available on</label>
                          <!-- <ValidationProvider v-slot="{ dirty }"> -->
                          <v-treeview
                            v-model="service.availability.avaiDays"
                            class="grey--text text--darken-1"
                            :items="daysOfWeek"
                            dense
                            selectable
                            open-on-click
                            transition
                            activatable
                            :active.sync="service.activeAvaiDays"
                            color="blue"
                            @input="checkServiceAvailability(
                              service.availability,
                              service.hasSelectedNewDay
                            )"
                            @update:active="updateAvaiDaysContxtView(
                              service.serviceName,
                              $event
                            )"
                          />
                        </v-col>

                        <v-divider vertical />

                        <!--  AVAILABLE-DAYS--CONTEXT-VIEW  -->
                        <v-col
                          class="d-flex text-center"
                        >
                          <v-scroll-y-transition mode="out-in">
                            <v-card
                              class="pt-6 mx-auto"
                              flat
                              max-width="400"
                            >
                              <v-card-text>
                                <div v-if="service.activeAvaiDays.length===0" class="mt-16 pt-6">
                                  <v-row justify="center" align="center">
                                    <v-col cols="10">
                                      <span class="title">
                                        <v-icon
                                          large
                                          left
                                        >
                                          mdi-arrow-left
                                        </v-icon>
                                        Select A Day
                                      </span>
                                    </v-col>
                                    <v-col cols="10">
                                      <span class="text-caption ml-8">
                                        by clicking on the name of day
                                      </span>
                                    </v-col>
                                  </v-row>
                                </div>

                                <div
                                  v-else-if="service.avaiDaysContxtView.isClosed &&
                                    service.activeAvaiDays.length!==0"
                                  class="mt-16 pt-6"
                                >
                                  <v-row justify="center" align="center">
                                    <v-col cols="10">
                                      <span class="title">
                                        <v-icon
                                          large
                                        >
                                          mdi-domain-off
                                        </v-icon>
                                        <pre class="title">Sorry, clinic is closed on {{ idxToDayOfWeek(service.serviceName) }}!</pre>
                                      </span>
                                    </v-col>
                                  </v-row>
                                </div>

                                <div v-else class="mt-n12">
                                  <span class="title">
                                    Adjust Operating Hours
                                  </span>

                                  <v-divider class="mb-2" />

                                  <span class="text-overline text-darken-4 pt-10">
                                    <strong>{{ idxToDayOfWeek(service.serviceName) }}</strong>
                                  </span>

                                  <v-row
                                    class="text-left mt-n6"
                                    tag="v-card-text"
                                  >
                                    <v-col class="text-center mr-4 mt-16" tag="strong" cols="5">
                                      <span>Start Hour</span>
                                      <ValidationProvider>
                                        <v-btn-toggle
                                          v-model="service.avaiDaysContxtView.startHourAMPM"
                                          mandatory
                                          tile
                                          color="blue"
                                          group
                                          @change="updateStartHourAMPM(
                                            service.serviceName,
                                            $event
                                          )"
                                        >
                                          <v-btn value="am">
                                            AM
                                          </v-btn>
                                          <v-btn value="pm">
                                            PM
                                          </v-btn>
                                        </v-btn-toggle>
                                      </ValidationProvider>
                                    </v-col>
                                    <v-col cols="5">
                                      <v-container>
                                        <ValidationProvider>
                                          <v-picker width="160">
                                            <v-time-picker-clock
                                              v-model="service.avaiDaysContxtView.startHour"
                                              :allowed-values="allowedHrs"
                                              :min="1"
                                              :max="12"
                                              :rotate="30"
                                              @change="updateStartHour(
                                                service.serviceName,
                                                $event
                                              )"
                                            >
                                              <div />
                                            </v-time-picker-clock>
                                          </v-picker>
                                        </ValidationProvider>
                                      </v-container>
                                    </v-col>

                                    <v-col class="text-center mr-4 mt-16" tag="strong" cols="5">
                                      <span>End Hour</span>
                                      <ValidationProvider>
                                        <v-btn-toggle
                                          v-model="service.avaiDaysContxtView.endHourAMPM"
                                          tile
                                          color="blue"
                                          group
                                          @change="updateEndHourAMPM(
                                            service.serviceName,
                                            $event
                                          )"
                                        >
                                          <v-btn value="am">
                                            AM
                                          </v-btn>
                                          <v-btn value="pm">
                                            PM
                                          </v-btn>
                                        </v-btn-toggle>
                                      </ValidationProvider>
                                    </v-col>
                                    <v-col cols="5">
                                      <v-container>
                                        <ValidationProvider>
                                          <v-picker width="160">
                                            <v-time-picker-clock
                                              v-model="service.avaiDaysContxtView.endHour"
                                              :min="1"
                                              :max="12"
                                              :rotate="30"
                                              @change="updateEndHour(
                                                service.serviceName,
                                                $event
                                              )"
                                            >
                                              <div />
                                            </v-time-picker-clock>
                                          </v-picker>
                                        </ValidationProvider>
                                      </v-container>
                                    </v-col>
                                  </v-row>
                                </div>
                              </v-card-text>
                            </v-card>
                          </v-scroll-y-transition>
                        </v-col>
                      </v-row>

                      <v-row no-gutters>
                        <v-col cols="20">
                          <v-divider />
                        </v-col>
                      </v-row>
                    </v-card-text>

                    <!--  SAVE  -->
                    <v-card-actions>
                      <v-row no-gutters justify="center">
                        <v-btn
                          text
                          :disabled="(!dirty &&
                            !service.hasAddedStaff.bool &&
                            !service.hasDeletedStaff.bool &&
                            !service.hasSelectedNewDay.bool) ||
                            invalid"
                          class="font-weight-medium grey--text text--darken-2 mt-n5 mb-n5"
                          @click="saveServiceDataToDirectory(
                            clinicName,
                            service.serviceName
                          )"
                        >
                          <v-icon left>
                            mdi-content-save
                          </v-icon>
                          Save
                        </v-btn>
                      </v-row>
                    </v-card-actions>
                  </v-card>
                </ValidationObserver>
              </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels>
        </keep-alive>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import {
  ValidationObserver,
  ValidationProvider
} from 'vee-validate'
import MaterialCard from '@/components/base/MaterialCard'

function parsePubHol (pubHolArr) {
  return pubHolArr.map((dateStr) => {
    const pubHol = dateStr.split(':')
    const pubHolYr = pubHol[0]
    const pubHolMth = pubHol[1]
    const pubHolDay = pubHol[2].split(',')

    return pubHolDay.map((dayStr) => {
      const res = [pubHolYr, pubHolMth, dayStr]
      return res.join('-')
    })
  }).reduce(twoDimArrToOneDimArr, [])
}

function twoDimArrToOneDimArr (collectorArr, arr) {
  for (let i = 0; i < arr.length; i++) {
    collectorArr.push(arr[i])
  }
  return collectorArr
}

function processStaffIdToTreeView (staffIds) {
  const tmpArr = []
  staffIds.sort()
  staffIds.map((val, idx, arr) => {
    val = val.split(':')
    tmpArr.push(
      {
        id: val[1],
        name: val[0]
      }
    )
  })
  return { id: 0, name: 'Select all', children: tmpArr }
}

function processOperatingDaysAndHours (avaiDaysStr, startHoursStr, endHoursStr) {
  const data = {}
  data.avaiDays = []
  data.startHours = []
  data.startHoursAMPM = []
  data.endHours = []
  data.endHoursAMPM = []
  const avaiDaysInt = avaiDaysStr.split(',').map(val => parseInt(val))
  const startHoursInt = startHoursStr.split(',').map(val => parseInt(val))
  const endHoursInt = endHoursStr.split(',').map(val => parseInt(val))

  for (let i = 0; i < 7; i++) {
    if (avaiDaysInt[i]) { data.avaiDays.push(i) }

    if (startHoursInt[i] <= 12) {
      data.startHours.push(startHoursInt[i])
    } else {
      data.startHours.push((startHoursInt[i] - 12))
    }

    if (startHoursInt[i] < 12) {
      data.startHoursAMPM.push('am')
    } else {
      data.startHoursAMPM.push('pm')
    }

    if (endHoursInt[i] <= 12) {
      data.endHours.push(endHoursInt[i])
    } else {
      data.endHours.push((endHoursInt[i] - 12))
    }

    if (endHoursInt[i] < 12) {
      data.endHoursAMPM.push('am')
    } else {
      data.endHoursAMPM.push('pm')
    }
  }

  return data
}

export default {
  components: {
    ValidationObserver,
    ValidationProvider,
    BaseMaterialCard: MaterialCard
  },
  async asyncData (context) {
    const { data } = await context.$axios.get('http://localhost:8080/clinicadm')
    let closeDays = data.clinic.clinicCloseDays
    closeDays = closeDays.split(',').map(val => parseInt(val))
    const servicesOriData = data.clinic.services
    const services = [...servicesOriData]
    for (let i = 0; i < services.length; i++) {
      // Service Staff
      services[i].hint = { msg: '' }
      services[i].clinicServiceStaffIds = [[processStaffIdToTreeView(servicesOriData[i].clinicServiceStaffIds)]]
      services[i].hasAddedStaff = { bool: false }
      services[i].hasDeletedStaff = { bool: false }

      // Service Operational Availability
      services[i].hasSelectedNewDay = { bool: false, sw: false }
      services[i].activeAvaiDays = []
      services[i].availability = processOperatingDaysAndHours(
        servicesOriData[i].availableDays,
        servicesOriData[i].startHour,
        servicesOriData[i].endHour
      )
      services[i].avaiDaysContxtView = {
        isClosed: false,
        startHour: '',
        endHour: '',
        startHourAMPM: 'am',
        endHourAMPM: 'pm'
      }

      // Search Related
      services[i].search = {
        clinicServiceStaffToAdd: null,
        clinicServiceStaffToDel: null
      }
      services[i].clinicServiceStaffToDel = []
      services[i].activeClinicServiceStaff = []
    }

    return {
      // CLINIC-SECTION
      staffAndClinicInfo: data,
      clinicName: data.clinic.clinicName,
      clinicState: data.clinic.clinicState,
      clinicDistrict: data.clinic.clinicDistrict,
      clinicCloseDays: closeDays,
      clinicPubHol: parsePubHol(data.clinic.clinicPublicHolidays),
      clinicStaff: [[processStaffIdToTreeView(data.clinic.clinicStaffIds)]],
      clinicHasAddedStaff: { bool: false },
      clinicHasDeletedStaff: { bool: false },

      // SERVICE-SECTION
      clinicServices: services
    }
  },

  data () {
    return {
      curYear: new Date().getFullYear(),
      daysOfWeek: [
        {
          id: 0,
          name: 'Sunday'
        },
        {
          id: 1,
          name: 'Monday'
        },
        {
          id: 2,
          name: 'Tuesday'
        },
        {
          id: 3,
          name: 'Wednesday'
        },
        {
          id: 4,
          name: 'Thursday'
        },
        {
          id: 5,
          name: 'Friday'
        },
        {
          id: 6,
          name: 'Saturday'
        }
      ],
      newStaffName: '',
      newStaffId: '',
      newStaffStatusHint: { msg: '' },
      addNewStaffInputValid: false,
      searchClinicStaffToDel: null,
      clinicStaffToDel: [],
      activeClinicStaff: [],
      nameRules: [
        v => !!v || 'Name is required'
      ],
      idRules: [
        v => !!v || 'ID is required'
      ],
      allowedHrs: v => [12, 1, 2].includes(v)
    }
  },
  computed: {
    // Do nothing for now
  },
  methods: {
    checkServiceAvailability (serviceAvailability, hasSelectedNewDay) {
      // This block is to prevent this
      // function from executing twice triggered
      // reactively when we removed the forbidden
      // day from 'serviceAvailability.avaiDays'.
      if (hasSelectedNewDay.sw) {
        hasSelectedNewDay.sw = false
        return
      }

      const tmpArr = [...serviceAvailability.avaiDays]
      serviceAvailability.avaiDays = this.leftSymDiff(tmpArr, this.clinicCloseDays)
      const symDiffRes = this.symDiff(serviceAvailability.avaiDays, tmpArr)
      if (symDiffRes.length !== 0) { /* we have pick a new day but forbidden tht got removed */
        hasSelectedNewDay.bool = false
        hasSelectedNewDay.sw = true
      } else { /* we have pick a new day and allowed */
        hasSelectedNewDay.bool = true
      }
    },
    symDiff (arr1, arr2) {
      return arr1.filter(x => !arr2.includes(x)).concat(
        arr2.filter(x => !arr1.includes(x))
      )
    },
    leftSymDiff (arr1, arr2) {
      return arr1.filter(x => !arr2.includes(x))
    },
    saveClinicDataToDirectory (clinicName) {
      const output = {}

      output.clinicName = this.clinicName
      output.clinicState = this.clinicState
      output.clinicDistrict = this.clinicDistrict
      output.clinicCloseDays = this.processCloseDaysIntArrToStr(this.clinicCloseDays)
      output.clinicPublicHolidays = this.processPubHolNuxtToOpendjFormat(
        this.clinicPubHol,
        this.curYear)
      output.clinicStaffIds = this.processTreeViewToClinicStaffId()

      const data = JSON.stringify(output)
      // eslint-disable-next-line
      console.log(output)
      // eslint-disable-next-line
      console.log(data)
      this.$axios.post('http://localhost:8081/updateclinic', data)
        .then((response) => {
          for (let i = 0; i < this.clinicServices.length; i++) {
            const avaiDays = [...this.clinicServices[i].availability.avaiDays]
            this.clinicServices[i].availability.avaiDays = this.leftSymDiff(avaiDays, this.clinicCloseDays)
            this.saveServiceDataToDirectory(
              this.clinicName,
              this.clinicServices[i].serviceName
            )
          }
          alert('saveClinicDataToDirectory done successfully!')
        }, (response) => {
          alert('saveClinicDataToDirectory failed!')
        })
    },
    processCloseDaysIntArrToStr (closeDaysArr) {
      return closeDaysArr.map(val => val.toString()).join(',')
    },
    processPubHolNuxtToOpendjFormat (dates, curYear) {
      // Use the structure of Javascript Obj to
      // store the year and month value as its key recursively.
      // If year and month key is absent, then create it
      // by assigning it to '{}' empty object and '[]'
      // empty array respectively.
      // Month array is used to store the days.
      const res = {}
      dates.map((val) => {
        const tmp1 = val.split('-')
        if (parseInt(tmp1[0]) < curYear) {
          return
        }
        if (!res[tmp1[0]]) {
          res[tmp1[0]] = {}
        }
        if (!res[tmp1[0]][tmp1[1]] || res[tmp1[0]][tmp1[1]].length === 0) {
          res[tmp1[0]][tmp1[1]] = []
        }
        res[tmp1[0]][tmp1[1]].push(tmp1[2])
      }
      )
      // Now access the years and months key in the
      // booking Javascript object by using 'Object.keys()' function.
      // Keys are returned as string.
      // Join the days string together, then put the year, month and days
      // string as element in an array.
      // Join the string elements in the array to produce the final desired
      // output format ['<year>:<month>:<comma-delimited-days>', [...]]
      // (e.g ['2020:03:02,04,06', '2020:10:01,10,15'])
      const res2 = []
      const yrs = Object.keys(res)
      for (let i = 0; i < yrs.length; i++) {
        const mths = Object.keys(res[yrs[i]])
        for (let j = 0; j < mths.length; j++) {
          const tmp2 = [yrs[i], mths[j], res[yrs[i]][mths[j]].join(',')]
          res2.push(tmp2.join(':'))
        }
      }
      return res2
    },
    processTreeViewToClinicStaffId () {
      const staffIds = this.clinicStaff[0][0].children

      return staffIds.map((val) => {
        const tmpArr = [val.name, val.id]
        return tmpArr.join(':')
      })
    },
    saveServiceDataToDirectory (clinicName, serviceName) {
      const output = {}

      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === serviceName) {
          output.clinicName = clinicName
          output.serviceName = serviceName
          output.maxPtAtATime = parseInt(this.clinicServices[i].maxPtAtATime)
          output.avgConsultTime = parseInt(this.clinicServices[i].avgConsultTime)
          output.availableDays = this.processOperatingDays(serviceName)
          output.startHour = this.processOperatingStartHours(serviceName)
          output.endHour = this.processOperatingEndHours(serviceName)
          output.clinicServiceStaffIds = this.processTreeViewToServiceStaffId(serviceName)
          output.numOfStaff = output.clinicServiceStaffIds.length

          const data = JSON.stringify(output)
          // eslint-disable-next-line
          console.log(output)
          // eslint-disable-next-line
          console.log(data)
          this.$axios.post('http://localhost:8081/updateservice', data)
            .then((response) => {
              alert('Form submitted successfully!')
            }, (response) => {
              alert('Form submit failed!')
            })
        }
      }
    },
    processTreeViewToServiceStaffId (service) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const staffIds = this.clinicServices[i].clinicServiceStaffIds[0][0].children

          return staffIds.map((val) => {
            const tmpArr = [val.name, val.id]
            return tmpArr.join(':')
          })
        }
      }
    },
    processOperatingDays (service) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const tmpAvaiDays = ['0', '0', '0', '0', '0', '0', '0']
          const avaiDays = this.clinicServices[i].availability.avaiDays

          for (let j = 0; j < avaiDays.length; j++) {
            tmpAvaiDays[avaiDays[j]] = '1'
          }

          return tmpAvaiDays.join(',')
        }
      }
    },
    processOperatingStartHours (service) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const startHours = [...this.clinicServices[i].availability.startHours]
          const startHoursAMPM = this.clinicServices[i].availability.startHoursAMPM

          for (let j = 0; j < startHoursAMPM.length; j++) {
            if (startHoursAMPM[j] === 'pm' && startHours[j] !== 12) {
              startHours[j] += 12
            }
          }
          const tmpStartHours = startHours.map(val => val.toString())
          return tmpStartHours.join(',')
        }
      }
    },
    processOperatingEndHours (service) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const endHours = [...this.clinicServices[i].availability.endHours]
          const endHoursAMPM = this.clinicServices[i].availability.endHoursAMPM

          for (let j = 0; j < endHoursAMPM.length; j++) {
            if (endHoursAMPM[j] === 'pm' && endHours[j] !== 12) {
              endHours[j] += 12
            }
          }
          const tmpEndHours = endHours.map(val => val.toString())
          return tmpEndHours.join(',')
        }
      }
    },
    sortBy (prop) {
      this.projects.sort((a, b) => a[prop] < b[prop] ? -1 : 1)
    },
    idxToDayOfWeek (service) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const activeAvaiDays = this.clinicServices[i].activeAvaiDays
          return this.daysOfWeek[activeAvaiDays].name
        }
      }
    },
    addStaffToClinic (statusHint) {
      const existingStaff = this.clinicStaff[0][0].children
      for (let j = 0; j < existingStaff.length; j++) {
        if (existingStaff[j].id === this.newStaffId) {
          statusHint.msg = 'This staff is already registered in the clinic!'
          return
        }
      }
      statusHint.msg = `${this.newStaffName} added to the clinic`

      const newStaff = {
        id: this.newStaffId,
        name: this.newStaffName
      }
      existingStaff.push(newStaff)
      if (!this.clinicHasAddedStaff.bool) {
        this.clinicHasAddedStaff.bool = true
      }
      existingStaff.sort((a, b) => a.name > b.name)
      this.activeClinicStaff.push(this.newStaffId)
    },
    delStaffFrmClinic () {
      if (this.clinicStaffToDel.length === 0) {
        return
      }
      const staffIdToDelArr = [...this.clinicStaffToDel]
      const staffArr = this.clinicStaff[0][0].children
      for (let i = 0; i < staffIdToDelArr.length; i++) {
        for (let j = 0; j < staffArr.length; j++) {
          if (staffArr[j].id === staffIdToDelArr[i]) {
            staffArr.splice(j, 1)
            if (!this.clinicHasDeletedStaff.bool) {
              this.clinicHasDeletedStaff.bool = true
            }
            break
          }
        }
      }
    },
    addStaffToClinicService (service, searchHint, staffIds) {
      const existingStaff = staffIds[0][0].children

      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const staffToAdd = this.clinicServices[i].search.clinicServiceStaffToAdd
          if (staffToAdd === null || staffToAdd === undefined) {
            return
          }
          for (let j = 0; j < existingStaff.length; j++) {
            if (existingStaff[j].id === staffToAdd.id) {
              searchHint.msg = 'This staff is already in the service!'
              return
            }
          }
          searchHint.msg = `${staffToAdd.name} added to the service`
          existingStaff.push(staffToAdd)
          existingStaff.sort((a, b) => a.name > b.name)
          this.clinicServices[i].activeClinicServiceStaff.push(staffToAdd.id)
          if (!this.clinicServices[i].hasAddedStaff.bool) {
            this.clinicServices[i].hasAddedStaff.bool = true
          }
          return
        }
      }
    },
    delStaffFrmClinicService (service, staffIds) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const staffToDel = this.clinicServices[i].clinicServiceStaffToDel

          if (staffToDel.length === 0) {
            return
          }
          const staffIdToDelArr = [...staffToDel]
          const staffArr = staffIds[0][0].children
          for (let k = 0; k < staffIdToDelArr.length; k++) {
            for (let j = 0; j < staffArr.length; j++) {
              if (staffArr[j].id === staffIdToDelArr[k]) {
                staffArr.splice(j, 1)
                if (!this.clinicServices[i].hasDeletedStaff.bool) {
                  this.clinicServices[i].hasDeletedStaff.bool = true
                }
                break
              }
            }
          }
        }
      }
    },
    updateAvaiDaysContxtView (service, event) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const contxtView = this.clinicServices[i].avaiDaysContxtView

          if (this.clinicCloseDays.includes(parseInt(event))) {
            contxtView.isClosed = true
            return
          } else {
            contxtView.isClosed = false
          }

          contxtView.startHourAMPM = this.clinicServices[i].availability.startHoursAMPM[event]
          contxtView.startHour = this.clinicServices[i].availability.startHours[event]
          contxtView.endHourAMPM = this.clinicServices[i].availability.endHoursAMPM[event]
          contxtView.endHour = this.clinicServices[i].availability.endHours[event]
        }
      }
    },
    updateStartHourAMPM (service, event) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const activeAvaiDays = this.clinicServices[i].activeAvaiDays
          this.clinicServices[i].availability.startHoursAMPM[activeAvaiDays] = event
        }
      }
    },
    updateStartHour (service, event) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const activeAvaiDays = this.clinicServices[i].activeAvaiDays
          this.clinicServices[i].availability.startHours[activeAvaiDays] = event
        }
      }
    },
    updateEndHourAMPM (service, event) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const activeAvaiDays = this.clinicServices[i].activeAvaiDays
          this.clinicServices[i].availability.endHoursAMPM[activeAvaiDays] = event
        }
      }
    },
    updateEndHour (service, event) {
      for (let i = 0; i < this.clinicServices.length; i++) {
        if (this.clinicServices[i].serviceName === service) {
          const activeAvaiDays = this.clinicServices[i].activeAvaiDays
          this.clinicServices[i].availability.endHours[activeAvaiDays] = event
        }
      }
    },
    dateClick (date) {
      // Do nothing for now
    },
    allowedDates: val => parseInt(val.split('-')[2], 10) % 2 === 0
  }
}

</script>
