<template>
  <v-container
    fluid
    class="my-5"
    tag="section"
  >
    <div class="text-center text-h1 font-weight-bold mt-10 mb-4">
      <span class="blue--text text--lighten-1">m</span><span class="blue--text text--lighten-2">y</span><span class="blue--text text--lighten-1">TCA</span>
    </div>

    <div class="text-center text-h4 font-weight-regular mb-16">
      <span class="red--text text--darken-4">Booking</span>
    </div>

    <!-- <h1> {{ dailyOpSchedules }} </h1>
    <h1> {{ service }} </h1>
    <h1> {{ selectedClinicMeta }} </h1>
    <h1> {{ startOpHrs }} </h1>
    <h1> {{ endOpHrs }} </h1> -->
    <!-- <h1> {{ svcStartOpHrs }} </h1>
    <h1> {{ svcEndOpHrs }} </h1>
    <h1> {{ opHrDisplays }} </h1> -->
    <!-- <h1> {{ serviceDayOfWeek }} </h1>
    <h1> {{ queuesUsgPct }} </h1> -->

    <v-row justify="center" class="mx-3">
      <v-col cols="12" sm="6" md="4">
        <v-autocomplete
          v-model="clinic"
          :items="clinics"
          outlined

          label="Select klinik kesihatan"
          item-text="name"
          item-value="value"
          @change="clinicSelected"
        >
          <template v-slot:item="data">
            <v-list-item-content>
              <v-list-item-title class="font-weight-medium" v-text="data.item.name" />
              <v-list-item-subtitle class="caption" v-text="data.item.group" />
            </v-list-item-content>
          </template>
        </v-autocomplete>
      </v-col>
    </v-row>

    <div id="selectService" />
    <div v-if="clinic">
      <v-divider />

      <div class="text-center text-h6 my-6">
        Select a service
      </div>

      <v-row justify="center">
        <v-btn-toggle
          v-model="selectedService"
          tile
          color="success"
          group
          dense
          class="ml-16"
          @change="serviceSelected"
        >
          <v-row dense class="d-flex flex-wrap mx-16 px-16">
            <v-col cols="12">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Fever
              </div>
            </v-col>

            <v-col v-for="svc in feverSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Respiratory
              </div>
            </v-col>

            <v-col v-for="svc in respiratorySvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Non-Respiratory
              </div>
            </v-col>

            <v-col v-for="svc in nonrespiratorySvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Chronic Condition
              </div>
            </v-col>

            <v-col v-for="svc in chronicConditionSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Procedure
              </div>
            </v-col>

            <v-col v-for="svc in procedureSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Imaging
              </div>
            </v-col>

            <v-col v-for="svc in imagingSvcIcons" :key="svc.value" cols="auto">
              <v-btn :disabled="!svc.isAvailable" :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Counselling
              </div>
            </v-col>

            <v-col v-for="svc in counsellingSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Screening
              </div>
            </v-col>

            <v-col v-for="svc in screeningSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Women, Mothers and Children
              </div>
            </v-col>

            <v-col v-for="svc in mchSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>

            <v-col cols="12 mt-4">
              <div class="text-left deep-orange--text text--darken-4 text-body-2 mt-2">
                Misc.
              </div>
            </v-col>

            <v-col v-for="svc in miscSvcIcons" :key="svc.value" cols="auto">
              <v-btn :value="svc.value" large :color="selectedService===svc.value ? 'success':'white'">
                <v-avatar left tile size="36">
                  <img :src="svc.img">
                </v-avatar>
                <span class="pl-2">{{ svc.name }}</span>
              </v-btn>
            </v-col>
          </v-row>
        </v-btn-toggle>
      </v-row>
    </div>

    <div id="apptDatePicker" />
    <div v-if="selectedService">
      <v-divider class="mt-10" />

      <div class="text-center text-h6 my-6">
        Pick a date
      </div>

      <v-row justify="center" class="mx-3">
        <v-date-picker
          v-model="date"
          :allowed-dates="allowedDates"
          elevation="2"
          @click:date="getClinicSvcBookings"
          @change="dateSelected"
        />
      </v-row>
    </div>

    <div id="apptSchedule" />
    <div v-if="selectedService && date">
      <br>
      <v-divider />

      <div class="text-center text-h6 mt-6">
        Appointment schedule on {{ date }}
      </div>
      <div class="text-center deep-orange--text text--darken-4 text-body-2 mt-2 mb-6">
        Book a date here for your next appointment
      </div>
    </div>

    <div v-if="selectedService && date && queuesUsgPerDay.length!==0">
      <v-card v-for="opHr in opHrDisplays[serviceDayOfWeek]" :key="opHr.idx" class="mx-auto mt-9" max-width="600">
        <v-card-title>
          <!-- <span class="font-weight-bold">{{ opHr }}</span> -->
          <span class="font-weight-bold">{{ opHr.time }}</span>
        </v-card-title>

        <v-card-subtitle>
          {{ DaysOfWeek[serviceDayOfWeek] }}
        </v-card-subtitle>

        <div class="mx-3">
          <v-progress-linear
            :value="queuesUsgPct[opHr.idx]"
            :color="getColor(queuesUsgPct[opHr.idx])"
            background-color="light-blue lighten-2"
            height="30"
          >
            <template v-slot="{ value }">
              <span class="text-h6 white--text">{{ Math.ceil(value) }}% booked</span>
            </template>
          </v-progress-linear>
        </div>

        <v-card-actions>
          <v-btn
            class="mx-auto mt-2"
            color="black"
            text
            outlined
            :disabled="queuesUsgPct[opHr.idx]===100"
            @click="makeBooking(opHr.idx)"
          >
            Book now
          </v-btn>
        </v-card-actions>
      </v-card>
    </div>
    <div v-else>
      <!-- <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br> -->
      <br><br><br><br><br><br><br>
    </div>
  </v-container>
</template>

<script>
import qs from 'qs'

// 'prepareClinicList' function creates and returns
// a data structure with the following format to
// be used by v-autocomplete to display a list
// of clinics for user to choose:
//
// clinics: [
//   { header: 'Pahang' },
//   { name: 'KK Maran', value: 'kk_maran', group: 'Maran' },
//   { name: 'KK Bandar Jengka', value: 'kk_bandar_jengka', group: 'Maran' },
//   { name: 'KK Jengka 2', value: 'kk_jengka2', group: 'Maran' },
//   { name: 'KK Jengka 22', value: 'kk_jengka22', group: 'Maran' },
//   { divider: true },
//   { header: 'Selangor' },
//   { name: 'KK Bandar Botanik', value: 'kk_bandar_botanik', group: 'Klang' },
//   { name: 'KK Pandamaran', value: 'kk_pandamaran', group: 'Klang' },
//   { name: 'KK Shah Alam', value: 'kk_shah_alam', group: 'Klang' },
//   { name: 'KK Port Klang', value: 'kk_port_klang', group: 'Klang' }
// ]
function prepareClinicList (clinicsDir) {
  const clinicsOutput = []

  for (let i = 0; i < clinicsDir.states.length; i++) {
    const state = clinicsDir.states[i].name
    const districts = clinicsDir.states[i].districts

    clinicsOutput.push({ header: state })

    if (districts === null) {
      if (i !== (clinicsDir.states.length - 1)) {
        clinicsOutput.push({ divider: true })
      }
      continue
    }

    for (let j = 0; j < districts.length; j++) {
      const district = districts[j].name
      const clinics = districts[j].clinics

      if (clinics === null) { continue }

      for (let k = 0; k < clinics.length; k++) {
        const clinicMeta = clinics[k].id + ':' + district + ':' + state
        clinicsOutput.push({
          name: clinics[k].name, value: clinicMeta, group: district
        })
      }
    }

    if (i !== (clinicsDir.states.length - 1)) {
      clinicsOutput.push({ divider: true })
    }
  }

  return clinicsOutput
}

export default {
  components: {
  },

  async fetch () {
    const { data } = await this.$axios.get('http://localhost:8082/booking')
    this.dailyOpSchedules = data.dailyOpSchedule
  },

  async asyncData (context) {
    const { data } = await context.$axios.get('http://localhost:8082/clinics')

    return {
      clinics: prepareClinicList(data)
    }
  },

  data () {
    return {
      clinic: '',
      selectedService: '',
      // date: new Date().toISOString().substr(0, 10),
      date: '',
      startOpHrs: [],
      endOpHrs: [],
      svcStartOpHrs: [],
      svcEndOpHrs: [],
      opHrDisplays: [],
      serviceDayOfWeek: undefined,
      DaysOfWeek: {
        0: 'Sunday',
        1: 'Monday',
        2: 'Tuesday',
        3: 'Wednesday',
        4: 'Thursday',
        5: 'Friday',
        6: 'Saturday'
      },
      queuesCapPerDay: [],
      queuesUsgPerDay: [],
      progressValue: 70,
      dailyOpSchedules: '',
      feverSvcIcons: [
        { name: 'Fever', value: 'fever', img: '/Medical/Fever/fever2.svg' }
      ],
      respiratorySvcIcons: [
        { name: 'Cough/Runny Nose', value: 'urti', img: '/Medical/URTI/sneeze.svg' },
        { name: 'Asthma', value: 'asthma', img: '/Medical/Asthma/asthma.svg' },
        { name: 'Tuberculosis', value: 'tuberculosis', img: '/Medical/Tuberculosis/tuberculosis.svg' }
      ],
      nonrespiratorySvcIcons: [
        { name: 'Abdominal Discomfort', value: 'abdDiscomfort', img: '/Medical/AbdDiscomfort/cramps.svg' },
        { name: 'Vomiting/Diarrhoea', value: 'age', img: '/Medical/AGE/vomit.svg' }
      ],
      chronicConditionSvcIcons: [
        { name: 'Diabetes', value: 'diabetes', img: '/Medical/Diabetes/sugar-blood-level2.svg' },
        { name: 'Hypertension', value: 'hypertension', img: '/Medical/Hypertension/arm_blood_pressure.svg' },
        { name: 'PSY', value: 'psy', img: '/Medical/Psy/mental_health.svg' },
        { name: 'Quit Smoking Clinic', value: 'kbm', img: '/Medical/QuitSmoking/quit_smoking.svg' },
        { name: 'Methadone', value: 'methadone', img: '/Medical/DrugAddiction/methadone.svg' }
      ],
      procedureSvcIcons: [
        { name: 'Blood Taking', value: 'bloodTaking', img: '/Medical/BloodTaking/arm_blood_taking.svg' },
        { name: 'Wound Care', value: 'woundCare', img: '/Medical/WoundCare/wound.svg' }
      ],
      imagingSvcIcons: [
        { isAvailable: false, name: 'Fundoscopy', value: 'fundoscopy', img: '/Medical/EyeExamination/slit-lamp.svg' },
        { isAvailable: false, name: 'X-Ray', value: 'x-ray', img: '/Medical/Xray/xray.svg' }
      ],
      counsellingSvcIcons: [
        { name: 'Diet Counselling', value: 'dietCounselling', img: '/Medical/DietCounseling/food_pyramid.svg' }
      ],
      screeningSvcIcons: [
        { name: 'Pre-marital Screening', value: 'premaritalScreening', img: '/Medical/FamilyPlanning/wedding-rings.svg' },
        { name: 'Medical Checkup', value: 'medicalCheckup', img: '/Medical/MedicalCheckup/med_examination.svg' }
      ],
      mchSvcIcons: [
        { name: 'Antenatal Booking', value: 'antenatalBooking', img: '/Medical/Pregnancy/pregnancy_test.svg' },
        { name: 'Antenatal Follow-up', value: 'antenatalFUp', img: '/Medical/Pregnancy/ultrasonography.svg' },
        { name: 'Postnatal 1 Month', value: 'postnatal1Mth', img: '/Medical/FamilyPlanning/mother_and_baby.svg' },
        { name: 'Family Planning', value: 'familyPlanning', img: '/Medical/FamilyPlanning/family.svg' },
        { name: 'Pap Smear', value: 'papSmear', img: '/Medical/PapSmear/uterus.svg' },
        { name: 'Immunization', value: 'immunization', img: '/Medical/Immunization/immunization.svg' }
      ],
      miscSvcIcons: [
        { name: 'Other Problems', value: 'otherProblems', img: '/Medical/Ill/ill2.svg' }
      ]
    }
  },

  computed: {
    selectedClinicMeta () {
      const tmp = this.clinic.split(':')
      const clinicMeta = {}
      clinicMeta.clinicId = tmp[0]
      clinicMeta.district = tmp[1]
      clinicMeta.state = tmp[2]
      clinicMeta.service = this.selectedService
      return clinicMeta
    },

    queuesUsgPct () {
      const tmp = []
      if (this.queuesUsgPerDay === null) { return }
      for (let i = 0; i < this.queuesUsgPerDay.length; i++) {
        tmp.push(this.queuesUsgPerDay[i] / this.queuesCapPerDay[i] * 100)
      }
      return tmp
    }
  },

  watch: {
    selectedClinicMeta (val) {
      this.getClinicSvcOpHrs()
    },

    clinic (val) {
      if (this.clinic !== '' && this.selectedClinicMeta.clinicId) {
        this.updateAvaiSvcs()
      }
    }
  },

  methods: {
    async updateAvaiSvcs () {
      const payload = {}
      payload.clinicId = this.selectedClinicMeta.clinicId
      payload.district = this.selectedClinicMeta.district
      payload.state = this.selectedClinicMeta.state
      const payloadData = qs.stringify(payload)
      const { data } = await this.$axios.post('http://localhost:8082/checkservice', payloadData)

      const svcsChecked = data.svcsChecked
      svcsChecked.map((val) => {
        if (val.svcName === 'fundoscopy') {
          this.imagingSvcIcons[0].isAvailable = val.ifExist
        } else if (val.svcName === 'xray') {
          this.imagingSvcIcons[1].isAvailable = val.ifExist
        }
      })
    },

    clinicSelected () {
      document.querySelector('#selectService').scrollIntoView({ behavior: 'smooth' })
    },

    serviceSelected () {
      if (this.selectedService !== '') {
        document.querySelector('#apptDatePicker').scrollIntoView({ behavior: 'smooth' })
      }
    },

    dateSelected () {
      document.querySelector('#apptSchedule').scrollIntoView({ behavior: 'smooth' })
    },

    getColor (usgPct) {
      if (usgPct <= 50) {
        return 'blue lighten-1'
      } else if (usgPct <= 75) {
        return 'blue darken-1'
      } else if (usgPct <= 90) {
        return 'blue darken-2'
      } else {
        return 'blue darken-3'
      }
    },

    async getClinicSvcOpHrs () {
      if (this.selectedService === '') { return }
      const payloadData = qs.stringify(this.selectedClinicMeta)
      const { data } = await this.$axios.post('http://localhost:8082/clinicservice', payloadData)

      // TODO: Convert below 4 Vue instance variables to local variable
      this.startOpHrs = data.deptOpHrsMeta.startHrs.split(',').map(val => (parseInt(val)))
      this.endOpHrs = data.deptOpHrsMeta.endHrs.split(',').map(val => (parseInt(val)))
      this.svcStartOpHrs = data.svcOpHrsMeta.startHrs.split(',').map(val => (parseInt(val)))
      this.svcEndOpHrs = data.svcOpHrsMeta.endHrs.split(',').map(val => (parseInt(val)))

      this.opHrDisplays = []
      for (let i = 0; i < this.svcStartOpHrs.length; i++) {
        let deptHrLoopTracker = 0
        let dt = 0 /* Op Hour Display Tracker */
        const svcStartOpHr = this.svcStartOpHrs[i]
        const svcEndOpHr = this.svcEndOpHrs[i]
        const deptStartOpHr = this.startOpHrs[i]
        const deptEndOpHr = this.endOpHrs[i]
        const opHrDisplay = []
        let ampm = ' a.m.'

        for (let m = svcStartOpHr; m < svcEndOpHr; m++) {
          let k = deptHrLoopTracker
          let j = deptStartOpHr + deptHrLoopTracker
          for (; j < deptEndOpHr; k++, j++) {
            if (j === m) {
              deptHrLoopTracker = k + 1

              if (j >= 12) { ampm = ' p.m.' }
              if (j > 12) {
                opHrDisplay[dt] = {}
                opHrDisplay[dt].idx = k
                opHrDisplay[dt].time = (j - 12).toString() + ampm + ' - '
              } else {
                opHrDisplay[dt] = {}
                opHrDisplay[dt].idx = k
                opHrDisplay[dt].time = j.toString() + ampm + ' - '
              }

              if ((j + 1) === 12) { ampm = ' p.m.' }
              if (j + 1 > 12) {
                opHrDisplay[dt].time = opHrDisplay[dt].time +
                                      (j + 1 - 12).toString() + ampm
              } else {
                opHrDisplay[dt].time = opHrDisplay[dt].time +
                                      (j + 1).toString() + ampm
              }

              dt++
              break
            }
          }
        }
        this.opHrDisplays.push([...opHrDisplay])
      }

      // for (let i = 0; i < this.startOpHrs.length; i++) {
      //   const startOpHr = this.startOpHrs[i]
      //   const endOpHr = this.endOpHrs[i]
      //   // this.opHrDisplay = []
      //   const opHrDisplay = []
      //   let ampm = 'am'

      //   for (let k = 0, j = startOpHr; j < endOpHr; k++, j++) {
      //     if (j === 12) { ampm = 'pm' }
      //     if (j > 12) {
      //       opHrDisplay[k] = (j - 12).toString() + '00' + ampm + ' - '
      //     } else {
      //       opHrDisplay[k] = j.toString() + '00' + ampm + ' - '
      //     }

      //     if ((j + 1) === 12) { ampm = 'pm' }
      //     if (j + 1 > 12) {
      //       opHrDisplay[k] = opHrDisplay[k] +
      //                             (j + 1 - 12).toString() + '00' + ampm
      //     } else {
      //       opHrDisplay[k] = opHrDisplay[k] +
      //                             (j + 1).toString() + '00' + ampm
      //     }
      //   }
      //   this.opHrDisplays.push([...opHrDisplay])
      // }
    },

    async getClinicSvcBookings (date) {
      const payload = {}
      payload.clinicId = this.selectedClinicMeta.clinicId
      payload.service = this.selectedClinicMeta.service
      payload.date = date
      const payloadData = qs.stringify(payload)
      const { data } = await this.$axios.post('http://localhost:8082/clinicservicequeue', payloadData)

      this.serviceDayOfWeek = data.dayOfWeek
      this.queuesCapPerDay = data.queuesCapPerDay
      this.queuesUsgPerDay = data.queuesUsgPerDay
    },

    // allowedDates: val => parseInt(val.split('-')[2], 10) % 2 === 0,
    allowedDates: val => true,

    async makeBooking (opHourIndex) {
      const payload = {}
      payload.clinicId = this.selectedClinicMeta.clinicId
      payload.service = this.selectedService
      payload.date = this.date
      payload.opHrIdx = opHourIndex
      const payloadData = qs.stringify(payload)
      const { data } = await this.$axios.post('http://localhost:8082/book', payloadData)
      this.getClinicSvcBookings(this.date)
      alert(data.bookingRes)
    }
  }
}
</script>

<style>

/* .project.complete{
  border-left: 4px solid #3cd1c2;
}
.project.ongoing{
  border-left: 4px solid #ffaa2c;
}
.project.overdue{
  border-left: 4px solid #f83e70;
}
.v-chip.v-chip--no-color.complete{
  background: #3cd1c2;
}
.v-chip.v-chip--no-color.ongoing{
  background: #ffaa2c;
}
.v-chip.v-chip--no-color.overdue{
  background: #f83e70;
} */

</style>

<!-- <v-col cols="auto">
  <v-btn value="2" large :color="selectedService==='2' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Fever/fever2.svg">
    </v-avatar>
    <span class="pl-2">Fever</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="1" large :color="selectedService==='1' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/URTI/sneeze.svg">
    </v-avatar>
    <span class="pl-2">Cough/Runny Nose</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="3" large :color="selectedService==='3' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Asthma/asthma.svg">
    </v-avatar>
    <span class="pl-2">Asthma</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="4" large :color="selectedService==='4' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Tuberculosis/tuberculosis.svg">
    </v-avatar>
    <span class="pl-2">Tuberculosis</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="5" large :color="selectedService==='5' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/AbdDiscomfort/cramps.svg">
    </v-avatar>
    <span class="pl-2">Abdominal Discomfort</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="6" large :color="selectedService==='6' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/AGE/vomit.svg">
    </v-avatar>
    <span class="pl-2">Vomiting/Diarrhoea</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="7" large :color="selectedService==='7' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Diabetes/sugar-blood-level2.svg">
    </v-avatar>
    <span class="pl-2">Diabetes</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="8" large :color="selectedService==='8' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Hypertension/arm_blood_pressure.svg">
    </v-avatar>
    <span class="pl-2">Hypertension</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="9" large :color="selectedService==='9' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Psy/mental_health.svg">
    </v-avatar>
    <span class="pl-2">PSY</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="10" large :color="selectedService==='10' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/DrugAddiction/methadone.svg">
    </v-avatar>
    <span class="pl-2">Methadone</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="11" large :color="selectedService==='11' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/QuitSmoking/quit_smoking.svg">
    </v-avatar>
    <span class="pl-2">Quit Smoking Clinic</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="12" large :color="selectedService==='12' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/BloodTaking/arm_blood_taking.svg">
    </v-avatar>
    <span class="pl-2">Blood Taking</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="13" large :color="selectedService==='13' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/WoundCare/wound.svg">
    </v-avatar>
    <span class="pl-2">Wound Care</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn :disabled="!ifFundoscopy" value="14" large :color="selectedService==='14' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/EyeExamination/slit-lamp.svg">
    </v-avatar>
    <span class="pl-2">Fundoscopy</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn :disabled="!ifXray" value="15" large :color="selectedService==='15' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Xray/xray.svg">
    </v-avatar>
    <span class="pl-2">X-Ray</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="16" large :color="selectedService==='16' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/DietCounseling/food_pyramid.svg">
    </v-avatar>
    <span class="pl-2">Diet Counselling</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
    <v-btn value="17" large :color="selectedService==='17' ? 'success':''">
      <v-avatar left tile size="36">
        <img src="/Medical/FamilyPlanning/wedding-rings.svg">
      </v-avatar>
      <span class="pl-2">Pre-marital Screening</span>
    </v-btn>
  </v-col>

  <v-col cols="auto">
    <v-btn value="18" large :color="selectedService==='18' ? 'success':''">
      <v-avatar left tile size="36">
        <img src="/Medical/MedicalCheckup/med_examination.svg">
      </v-avatar>
      <span class="pl-2">Medical Checkup</span>
    </v-btn>
  </v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="19" large :color="selectedService==='19' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Pregnancy/pregnancy_test.svg">
    </v-avatar>
    <span class="pl-2">Antenatal Booking</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="20" large :color="selectedService==='20' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Pregnancy/ultrasonography.svg">
    </v-avatar>
    <span class="pl-2">Antenatal Follow-up</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="21" large :color="selectedService==='21' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/FamilyPlanning/mother_and_baby.svg">
    </v-avatar>
    <span class="pl-2">Postnatal 1 Month</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="22" large :color="selectedService==='22' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/FamilyPlanning/family.svg">
    </v-avatar>
    <span class="pl-2">Family Planning</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="23" large :color="selectedService==='23' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/PapSmear/uterus.svg">
    </v-avatar>
    <span class="pl-2">Pap Smear</span>
  </v-btn>
</v-col>

<v-col cols="auto">
  <v-btn value="24" large :color="selectedService==='24' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Immunization/immunization.svg">
    </v-avatar>
    <span class="pl-2">Immunization</span>
  </v-btn>
</v-col> -->

<!-- <v-col cols="auto">
  <v-btn value="25" large :color="selectedService==='25' ? 'success':''">
    <v-avatar left tile size="36">
      <img src="/Medical/Ill/ill2.svg">
    </v-avatar>
    <span class="pl-2">Other Problems</span>
  </v-btn>
</v-col> -->
