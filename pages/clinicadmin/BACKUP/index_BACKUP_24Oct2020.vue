<template>
  <v-container
    fluid
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
    <!-- DEPT LIST SECTION  -->
    <!-- ====================  -->
    <v-divider class="mt-14" />
    <div class="text-center text-h4 mt-6 mb-n4">
      Department
    </div>

    <v-row justify="center">
      <v-col cols="10">
        <keep-alive>
          <v-expansion-panels v-model="deptPanelToggleOpen" focusable class="mt-10" max-width="600">
            <v-expansion-panel
              v-for="service in clinicDepts"
              :key="service.deptName"
              class="mb-4"
              @click.native.capture="deptPanelClicked(service,
                                                      $event)"
            >
              <v-expansion-panel-header
                color="success"
                class="py-1"
              >
                <v-row align="center" class="ml-2 mr-4">
                  <span class="title font-weight-regular">
                    <v-icon
                      left
                      color="white"
                    >
                      mdi-hospital-box-outline
                    </v-icon>
                    <span class="white--text headline">
                      {{ service.deptName.toUpperCase() }}
                    </span>
                  </span>

                  <v-spacer />

                  <v-btn
                    :class="service.deptIsEnabled? 'green--text':'grey--text text--darken-3'"
                    :color="service.deptIsEnabled? 'white':'grey'"
                    @click.stop="deptPanelToggleEnable(service)"
                  >
                    Enable
                  </v-btn>

                  <template v-slot:actions>
                    <v-icon v-if="service.deptIsEnabled" color="white">
                      mdi-menu-down-outline
                    </v-icon>
                    <v-icon v-else disabled>
                      mdi-menu-down-outline
                    </v-icon>
                  </template>
                </v-row>
              </v-expansion-panel-header>
              <v-expansion-panel-content>
                <ValidationObserver :ref="`serviceObserver_${service.deptName}`" v-slot="{ dirty, invalid }">
                  <v-card
                    flat
                    rounded
                    class="rounded-lg"
                  >
                    <v-card-text class="font-weight-medium grey--text text--darken-4">
                      <v-row no-gutters class="mx-auto">
                        <!-- <v-col cols="5" class="text-left mt-4 ml-5">
                          <ValidationProvider v-slot="{ errors }" rules="required|numeric|min:1">
                            <v-text-field
                              v-model="avgConsultTime"
                              label="Average Consultation Time"
                              :error-messages="errors"
                              clear-icon="mdi-close-circle-outline"
                            />
                          </ValidationProvider>
                        </v-col> -->

                        <v-col cols="5" class="text-left mt-4 ml-10">
                          <ValidationProvider v-slot="{ errors }" rules="required|numeric|min:1">
                            <v-text-field
                              v-model="service.clinicDeptMaxPt"
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
                          <label>Add new staff to {{ service.deptName.toUpperCase() }} service</label>

                          <v-autocomplete
                            v-model="service.search.clinicDeptStaffToAdd"
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
                              :disabled="service.search.clinicDeptStaffToAdd===null ||
                                service.search.clinicDeptStaffToAdd===undefined"
                              class="font-weight-medium grey--text text--darken-2"
                              @click="addStaffToClinicDept(
                                service.deptName,
                                service.hint,
                                service.clinicDeptStaffIds
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
                            v-model="service.search.clinicDeptStaffToDel"
                            class="mt-2 ml-4"
                            label="Filter"
                            outlined
                            flat
                            hide-details
                            clearable
                          />

                          <v-virtual-scroll
                            :items="service.clinicDeptStaffIds"
                            :item-height="1000"
                            height="250"
                            class="ml-n2 mt-2"
                          >
                            <template v-slot="{ item }">
                              <v-list-item>
                                <v-list-item-content>
                                  <v-treeview
                                    v-model="service.clinicDeptStaffToDel"
                                    :items="item"
                                    :search="service.search.clinicDeptStaffToDel"
                                    open-all
                                    :open="service.treeviewRootAlwaysOpen"
                                    dense
                                    selectable
                                    activatable
                                    open-on-click
                                    :active.sync="service.activeClinicDeptStaff"
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
                              :disabled="service.clinicDeptStaffToDel.length===0"
                              class="font-weight-medium grey--text text--darken-2 mt-5"
                              @click="delStaffFrmClinicDept(
                                service.deptName,
                                service.clinicDeptStaffIds
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
                          <label>{{ service.deptName.toUpperCase() }} department is available on</label>
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
                              service.hasSelectedNewDay,
                              service
                            )"
                            @update:active="updateAvaiDaysContxtView(
                              service.deptName,
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
                                        <pre class="title">Sorry, clinic is closed on {{ idxToDayOfWeek(service.deptName) }}!</pre>
                                      </span>
                                    </v-col>
                                  </v-row>
                                </div>

                                <div
                                  v-else-if="!service.avaiDaysContxtView.isAvailable &&
                                    service.activeAvaiDays.length!==0"
                                  class="mt-16 pt-6"
                                >
                                  <v-row justify="center" align="center">
                                    <v-col cols="10">
                                      <span class="title">
                                        <v-icon
                                          large
                                        >
                                          mdi-sticker-check-outline
                                        </v-icon>
                                        <div class="title">Check the tickbox to activate {{ service.deptName }} department on {{ idxToDayOfWeek(service.deptName) }}</div>
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
                                    <strong>{{ idxToDayOfWeek(service.deptName) }}</strong>
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
                                            service.deptName,
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
                                                service.deptName,
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
                                            service.deptName,
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
                                                service.deptName,
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
                          :disabled="invalid"
                          class="font-weight-medium grey--text text--darken-2 mt-n5 mb-n5"
                        >
                          <v-icon left>
                            mdi-doctor
                          </v-icon>
                          <nuxt-link tag="span" to="/clinicadmin/service">
                            Manage Services
                          </nuxt-link>
                          <!-- <nuxt-link tag="span" :to="{path: '/clinicadmin/service', query: { hello: 'helloworld' }}">
                            Manage Services
                          </nuxt-link> -->
                        </v-btn>

                        <v-btn
                          text
                          :disabled="(!dirty &&
                            !service.hasAddedStaff.bool &&
                            !service.hasDeletedStaff.bool &&
                            !service.hasSelectedNewDay.isAllowed) ||
                            invalid"
                          class="font-weight-medium grey--text text--darken-2 mt-n5 mb-n5"
                          @click="saveDeptDataToDirectory(service.deptName)"
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
import { ValidationObserver, ValidationProvider } from 'vee-validate'
import MaterialCard from '@/components/base/MaterialCard'
import * as DeptConsts from '@/utils/constants/mytca_booking/DeptConsts'
import * as AdmPgFuncs from '@/utils/functions/AdminPageFuncs'
import * as Sets from '@/utils/functions/math/Sets'
import { mapMutations } from 'vuex'

export default {

  components: {
    ValidationObserver,
    ValidationProvider,
    BaseMaterialCard: MaterialCard
  },

  fetch () {
    this.setUserId('880601105150')
    this.setUserPwd('88motherfaker88')
    this.setState('pahang')
    this.setDistrict('maran')
    this.setClinicId('kk_maran')
  },

  async asyncData (context) {
    const userId = '880601105150'
    const userPwd = '88motherfaker88'
    const clinicId = 'kk_maran'
    const district = 'maran'
    const state = 'pahang'

    let clinicDetailsApi = 'http://localhost:8082/admin/clinicdetails'
    const queryStr = `?userId=${userId}&userPwd=${userPwd}&clinicId=${clinicId}&district=${district}&state=${state}`
    clinicDetailsApi = clinicDetailsApi + queryStr
    const { data } = await context.$axios.post(clinicDetailsApi)

    let closeDays = data.clinicCloseDays
    closeDays = closeDays.split(',').map(val => parseInt(val))

    const depts = []

    // LOAD DEPT BASIC DATA OBTAINED FROM DIRECTORY
    const clinicDeptBasicDetailsLst = data.clinicDeptBasicDetailsLst
    clinicDeptBasicDetailsLst.map((val) => {
      const deptObj = {}
      deptObj.deptName = val.clinicDeptName
      const boolVal = !!val.clinicDeptIsEnabled
      deptObj.deptIsEnabled = boolVal
      deptObj.isBasicDataLoaded = true
      deptObj.isAdvDataLoaded = false
      deptObj.isDirty = false
      depts.push(deptObj)
    })

    // INITIALIZE REMAINING UNACTIVATED DEPT WITH DEFAULT VALUE
    const deptsCpy = [...depts]
    for (let m = 0; m < DeptConsts.DEPT_ALL.length; m++) {
      let deptPresent = false
      const deptName = DeptConsts.DEPT_ALL[m]
      for (let n = 0; n < deptsCpy.length; n++) {
        if (deptsCpy[n].deptName === deptName) {
          deptPresent = true
          break
        }
      }
      if (!deptPresent) {
        const deptObj = {}
        deptObj.deptName = deptName
        deptObj.deptIsEnabled = false
        deptObj.isBasicDataLoaded = false
        deptObj.isAdvDataLoaded = false
        deptObj.isDirty = false
        depts.push(deptObj)
      }
    }

    // INITIALIZE OTHER PROPERTIES OF ALL dept OBJECT IN 'depts'
    for (let i = 0; i < depts.length; i++) {
      depts[i].treeviewRootAlwaysOpen = [0]
      // DEPT STAFF
      depts[i].hint = { msg: '' }
      depts[i].hasAddedStaff = { bool: false }
      depts[i].hasDeletedStaff = { bool: false }

      // DEPT OPERATIONAL AVAILABILITY
      // depts[i].deptIsEnabled = { bool: false }
      depts[i].isPanelFirstLoad = true
      depts[i].hasSelectedNewDay = { isAllowed: false, sw: false }
      depts[i].activeAvaiDays = []
      depts[i].avaiDaysContxtView = {
        isClosed: false,
        isAvailable: false,
        startHour: '',
        endHour: '',
        startHourAMPM: 'am',
        endHourAMPM: 'pm'
      }

      // SEARCH RELATED
      depts[i].search = {
        clinicDeptStaffToAdd: null,
        clinicDeptStaffToDel: null
      }
      depts[i].clinicDeptStaffToDel = []
      depts[i].activeClinicDeptStaff = []

      // MISC
      // depts[i].clinicDeptStaffIds = []
      depts[i].clinicDeptStaffIds = [[AdmPgFuncs.processStaffIdToTreeView([])]]
      // depts[i].availability = {}
      depts[i].availability = AdmPgFuncs.processOperatingDaysAndHours('', '', '')
      depts[i].clinicDeptMaxPt = 0
    }

    return {
      // CLINIC-SECTION
      clinicName: data.clinicName,
      clinicState: data.clinicState,
      clinicDistrict: data.clinicDistrict,
      clinicCloseDays: closeDays,
      clinicPubHol: AdmPgFuncs.parsePubHol(data.clinicPublicHolidays),
      clinicStaff: [[AdmPgFuncs.processStaffIdToTreeView(data.clinicStaffIds)]],
      clinicHasAddedStaff: { bool: false },
      clinicHasDeletedStaff: { bool: false },

      // DEPARTMENT-SECTION
      clinicDepts: depts
    }
  },

  data () {
    return {
      userId: '880601105150',
      userPwd: '88motherfaker88',
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
      deptPanelToggleOpen: null,
      avgConsultTime: 100,
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

  watch: {
    // Different DeptPanel interaction scenarios,
    // when clicking the panel expand button:
    // 1) IsBasicDataNotLoaded, Absent
    //    - [Implemented in SaveButton func]
    //    - Client app need to create a new dept. No SearchRequest needed.
    //    - When user click SaveButton, client app send the new dept data
    //      to websvc.
    //    - Websvc send AddRequest to save new dept data to directory.
    // 2) IsBasicDataLoaded, Present, IsAdvDataNotLoaded, IsNotDirty
    //    - [Implemented in THIS func]
    //    - Client app send a req to websvc.
    //    - Websvc send a SearchRequest to directory, get the
    //      dept AdvData, then send it back to client app.
    // 3) IsBasicDataLoaded, Present, IsAdvDataNotLoaded, IsDirty
    //    - [Implemented in THIS func]
    //    - Client app send a req and IsEnabled data to websvc.
    //    - Websvc send ModifyRequest to update IsEnabled data
    //      to directory.
    //    - Websvc then send a SearchRequest to directory, get the
    //      dept AdvData, then send it back to client app.
    // 4) IsBasicDataLoaded, Present, IsAdvDataLoaded, IsNotDirty
    //    - [Not implemented]
    //    - Client app does nothing.
    // 5) IsBasicDataLoaded, Present, IsAdvDataLoaded, IsDirty
    //    - [Implemented in SaveButton func]
    //    - Client app waits for user's action to save data.
    //    - When user click SaveButton, client app send dept data
    //      to websvc.
    //    - Websvc send ModifyRequest to save dept data to directory.
    async deptPanelToggleOpen (idx) {
      if (idx !== null && idx !== undefined) {
        if (this.clinicDepts[idx].isBasicDataLoaded &&
            !this.clinicDepts[idx].isAdvDataLoaded) {
          // LOAD DEPT ADVANCED DATA
          const userId = this.userId
          const userPwd = this.userPwd
          const deptName = this.clinicDepts[idx].deptName
          const clinicId = 'kk_maran'
          const district = this.clinicDistrict
          const state = this.clinicState
          const isDirty = this.clinicDepts[idx].isDirty
          const isEnabled = this.clinicDepts[idx].deptIsEnabled

          let clinicDeptDetailsApi = 'http://localhost:8082/admin/clinicdeptdetails'
          const queryStr = `?isDirty=${isDirty}&isEnabled=${isEnabled}&userId=${userId}&userPwd=${userPwd}&deptName=${deptName}&clinicId=${clinicId}&district=${district}&state=${state}`
          clinicDeptDetailsApi = clinicDeptDetailsApi + queryStr
          const { data } = await this.$axios.post(clinicDeptDetailsApi)

          // TODO: Add a spinning icon here and retry
          // $axios.post(clinicDeptDetailsApi) again if data is null/undefined
          this.clinicDepts[idx].clinicDeptMaxPt = data.clinicDeptMaxPt
          this.clinicDepts[idx].clinicDeptStaffIds = [[AdmPgFuncs.processStaffIdToTreeView(data.clinicDeptStaffIds)]]
          this.clinicDepts[idx].availability = AdmPgFuncs.processOperatingDaysAndHours(
            data.clinicDeptAvaiDays,
            data.clinicDeptStartHrs,
            data.clinicDeptEndHrs
          )

          this.clinicDepts[idx].isLoaded = true
          this.clinicDepts[idx].treeviewRootAlwaysOpen = [0]

          this.setDeptName(this.clinicDepts[idx].deptName)
          this.setDeptAvaiDays(this.clinicDepts[idx].availability.avaiDays)
          this.setStartHrs(this.clinicDepts[idx].availability.startHours)
          this.setStartHrsAMPM(this.clinicDepts[idx].availability.startHoursAMPM)
          this.setEndHrs(this.clinicDepts[idx].availability.endHours)
          this.setEndHrsAMPM(this.clinicDepts[idx].availability.endHoursAMPM)
        }

        this.setDeptName(this.clinicDepts[idx].deptName)
        if (this.clinicDepts[idx].availability.avaiDays.length === 0) {
          this.clinicDepts[idx].isPanelFirstLoad = false
        }
      }
    }
  },

  methods: {
    ...mapMutations('admin', {
      setUserId: 'setUserId',
      setUserPwd: 'setUserPwd',
      setState: 'setState',
      setDistrict: 'setDistrict',
      setClinicId: 'setClinicId',
      setDeptName: 'setDeptName',
      setDeptAvaiDays: 'setDeptAvaiDays',
      setStartHrs: 'setStartHrs',
      setStartHrsAMPM: 'setStartHrsAMPM',
      setEndHrs: 'setEndHrs',
      setEndHrsAMPM: 'setEndHrsAMPM'
    }),

    deptPanelClicked (dept, event) {
      if (event.target.textContent.length === 42) {
        // 42 is the textContent length for "Enable" text
        // of 'deptPanelToggleEnable' button
        return
      }
      if (dept.deptIsEnabled) {
        return
      }
      event.stopPropagation()
    },

    deptPanelToggleEnable (dept) {
      dept.deptIsEnabled = !dept.deptIsEnabled
      dept.isDirty = true
    },

    checkServiceAvailability (serviceAvailability, hasSelectedNewDay, dept) {
      // This block is to prevent this
      // function from executing twice, triggered
      // reactively when we removed the forbidden
      // day from 'serviceAvailability.avaiDays'.
      if (hasSelectedNewDay.sw) {
        hasSelectedNewDay.sw = false
        return
      }

      if (dept.isPanelFirstLoad) {
        dept.isPanelFirstLoad = false
      } else {
        const newlyCheckedOpt = Sets.leftSymDiff(serviceAvailability.avaiDays, serviceAvailability.prevAvaiDays)
        if (newlyCheckedOpt.length !== 0) {
          // We have checked a treeview option
          dept.activeAvaiDays = newlyCheckedOpt
          this.updateAvaiDaysContxtView(dept.deptName, dept.activeAvaiDays[0])
        } else {
          // We have unchecked a treeview option
          const newlyUncheckedOpt = Sets.leftSymDiff(serviceAvailability.prevAvaiDays, serviceAvailability.avaiDays)
          if (newlyUncheckedOpt[0] === dept.activeAvaiDays[0]) {
            // We have unchecked a selected active treeview option
            this.updateAvaiDaysContxtView(dept.deptName, dept.activeAvaiDays[0])
          }
        }
      }

      const tmpArr = [...serviceAvailability.avaiDays]
      serviceAvailability.avaiDays = Sets.leftSymDiff(tmpArr, this.clinicCloseDays)
      serviceAvailability.prevAvaiDays = serviceAvailability.avaiDays
      this.setDeptAvaiDays(serviceAvailability.avaiDays)
      const symDiffRes = Sets.symDiff(serviceAvailability.avaiDays, tmpArr)
      if (symDiffRes.length !== 0) { /* we have pick a new day but forbidden, and got removed from serviceAvailability.avaiDays */
        hasSelectedNewDay.isAllowed = false
        hasSelectedNewDay.sw = true
      } else { /* we have pick a new day and allowed */
        hasSelectedNewDay.isAllowed = true
      }
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
          for (let i = 0; i < this.clinicDepts.length; i++) {
            const avaiDays = [...this.clinicDepts[i].availability.avaiDays]
            this.clinicDepts[i].availability.avaiDays = Sets.leftSymDiff(avaiDays, this.clinicCloseDays)
            this.saveDeptDataToDirectory(
              this.clinicName,
              this.clinicDepts[i].serviceName
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

    saveDeptDataToDirectory (deptName) {
      const output = {}

      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          output.userId = this.userId
          output.userPwd = this.userPwd
          output.state = this.clinicState
          output.district = this.clinicDistrict
          output.clinicName = this.clinicName
          output.deptName = deptName
          output.deptIsEnabled = this.clinicDepts[i].deptIsEnabled ? '1' : '0'
          output.deptAvaiDays = this.processOperatingDays(deptName)
          output.deptStartHrs = this.processOperatingStartHours(deptName)
          output.deptEndHrs = this.processOperatingEndHours(deptName)
          output.deptStaffIds = this.processTreeViewToServiceStaffId(deptName)
          output.deptNumOfStaff = output.deptStaffIds.length.toString()
          output.deptMaxPt = this.clinicDepts[i].clinicDeptMaxPt.toString()

          const data = JSON.stringify(output)

          if (this.clinicDepts[i].isBasicDataLoaded) {
            // UPDATE DEPT
            this.$axios.post('http://localhost:8082/admin/updatedept', data)
              .then((response) => {
                alert('Dept data updated successfully!')
              }, (response) => {
                alert('Dept data update failed!')
              })
          } else {
            // ADD A NEW DEPT
            this.$axios.post('http://localhost:8082/admin/adddept', data)
              .then((response) => {
                alert('New dept added successfully!')
                this.clinicDepts[i].isBasicDataLoaded = true
              }, (response) => {
                alert('New dept add failed!')
              })
          }

          this.setDeptAvaiDays(this.clinicDepts[i].availability.avaiDays)
          this.setStartHrs(this.clinicDepts[i].availability.startHours)
          this.setStartHrsAMPM(this.clinicDepts[i].availability.startHoursAMPM)
          this.setEndHrs(this.clinicDepts[i].availability.endHours)
          this.setEndHrsAMPM(this.clinicDepts[i].availability.endHoursAMPM)
        }
      }
    },

    processTreeViewToServiceStaffId (deptName) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const staffIds = this.clinicDepts[i].clinicDeptStaffIds[0][0].children

          return staffIds.map((val) => {
            const tmpArr = [val.name, val.id]
            return tmpArr.join(':')
          })
        }
      }
    },

    processOperatingDays (deptName) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const tmpAvaiDays = ['0', '0', '0', '0', '0', '0', '0']
          const avaiDays = this.clinicDepts[i].availability.avaiDays

          for (let j = 0; j < avaiDays.length; j++) {
            tmpAvaiDays[avaiDays[j]] = '1'
          }

          return tmpAvaiDays.join(',')
        }
      }
    },

    processOperatingStartHours (deptName) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const startHours = [...this.clinicDepts[i].availability.startHours]
          const startHoursAMPM = this.clinicDepts[i].availability.startHoursAMPM

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

    processOperatingEndHours (deptName) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const endHours = [...this.clinicDepts[i].availability.endHours]
          const endHoursAMPM = this.clinicDepts[i].availability.endHoursAMPM

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

    idxToDayOfWeek (deptName) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const activeAvaiDays = this.clinicDepts[i].activeAvaiDays[0]
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

    addStaffToClinicDept (deptName, searchHint, staffIds) {
      const existingStaff = staffIds[0][0].children

      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const staffToAdd = this.clinicDepts[i].search.clinicDeptStaffToAdd
          if (staffToAdd === null || staffToAdd === undefined) {
            return
          }
          for (let j = 0; j < existingStaff.length; j++) {
            if (existingStaff[j].id === staffToAdd.id) {
              searchHint.msg = `${staffToAdd.name} is already in the department!`
              return
            }
          }
          existingStaff.push(staffToAdd)
          searchHint.msg = `${staffToAdd.name} added to the department`
          existingStaff.sort((a, b) => a.name > b.name)
          this.clinicDepts[i].activeClinicDeptStaff.push(staffToAdd.id)
          if (!this.clinicDepts[i].hasAddedStaff.bool) {
            this.clinicDepts[i].hasAddedStaff.bool = true
          }
          this.clinicDepts[i].treeviewRootAlwaysOpen = [0]
          return
        }
      }
    },

    delStaffFrmClinicDept (deptName, staffIds) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const staffToDel = this.clinicDepts[i].clinicDeptStaffToDel

          if (staffToDel.length === 0) {
            return
          }
          const staffIdToDelArr = [...staffToDel]
          const staffArr = staffIds[0][0].children
          for (let k = 0; k < staffIdToDelArr.length; k++) {
            for (let j = 0; j < staffArr.length; j++) {
              if (staffArr[j].id === staffIdToDelArr[k]) {
                staffArr.splice(j, 1)
                if (!this.clinicDepts[i].hasDeletedStaff.bool) {
                  this.clinicDepts[i].hasDeletedStaff.bool = true
                }
                break
              }
            }
          }
        }
      }
    },

    updateAvaiDaysContxtView (deptName, eventSelectedDay) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const contxtView = this.clinicDepts[i].avaiDaysContxtView

          if (this.clinicCloseDays.includes(parseInt(eventSelectedDay))) {
            contxtView.isClosed = true
            return
          } else {
            if (this.clinicDepts[i].availability.avaiDays.includes(parseInt(eventSelectedDay))) {
              contxtView.isAvailable = true
            } else {
              contxtView.isAvailable = false
            }
            contxtView.isClosed = false
          }

          contxtView.startHourAMPM = this.clinicDepts[i].availability.startHoursAMPM[eventSelectedDay]
          contxtView.startHour = this.clinicDepts[i].availability.startHours[eventSelectedDay]
          contxtView.endHourAMPM = this.clinicDepts[i].availability.endHoursAMPM[eventSelectedDay]
          contxtView.endHour = this.clinicDepts[i].availability.endHours[eventSelectedDay]
        }
      }
    },

    updateStartHourAMPM (deptName, eventStartHrAMPM) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const activeAvaiDays = this.clinicDepts[i].activeAvaiDays
          this.clinicDepts[i].availability.startHoursAMPM[activeAvaiDays] = eventStartHrAMPM
        }
      }
    },

    updateStartHour (deptName, eventStartHr) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const activeAvaiDays = this.clinicDepts[i].activeAvaiDays
          this.clinicDepts[i].availability.startHours[activeAvaiDays] = eventStartHr
        }
      }
    },

    updateEndHourAMPM (deptName, eventEndHrAMPM) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const activeAvaiDays = this.clinicDepts[i].activeAvaiDays
          this.clinicDepts[i].availability.endHoursAMPM[activeAvaiDays] = eventEndHrAMPM
        }
      }
    },

    updateEndHour (deptName, eventEndHr) {
      for (let i = 0; i < this.clinicDepts.length; i++) {
        if (this.clinicDepts[i].deptName === deptName) {
          const activeAvaiDays = this.clinicDepts[i].activeAvaiDays
          this.clinicDepts[i].availability.endHours[activeAvaiDays] = eventEndHr
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
