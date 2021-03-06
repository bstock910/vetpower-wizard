<template>
  <div class="column is-8 is-offset-2">
    <form>
      <h5 class="subtitle is-5 has-text-centered is-large">Schedule Selection</h5>
      <div class="is-divider"></div>
      <div v-if="software === 'AVIMark'">
        <p class="subtitle has-text-centered is-medium">How do you manage your appointment calendar?</p>
        <div class="level">
          <label class="radio">
            <input type="radio" id="true" v-model="scheduleByProvider" :value="true"> By provider <button v-tooltip="{
  content: 'Choose this option if the schedule is managed by doctor or provider (John Smith DVM, Jane Doe DVM, Technician, etc.)',
  placement: 'right-end',
  classes: ['tooltip-info'],
  targetClasses: ['it-has-a-tooltip']
}">Button</button>
          </label>
        </div>
        <div class="level">
          <label class="radio">
            <input type="radio" id="false" v-model="scheduleByProvider" :value="false"> By room <button v-tooltip="{
  content: 'Choose this option if the schedule is managed by room (Exam Room 1, Grooming, Surgery, etc.)',
  placement: 'right-start',
  classes: ['tooltip-info'],
  targetClasses: ['it-has-a-tooltip']
}">Button</button>
          </label>
        </div>
      </div>
      <div v-if="scheduleByProvider">
        <h5 class="subtitle has-text-centered is-medium">Choose providers available for scheduling</h5>
        <div class="columns is-multiline">
          <div v-for="provider in sortedProviders" :key="provider.id" class="column is-4">
            <input type="checkbox" class="checkbox" :value="provider" v-model="providersUsed">
            {{provider.name}} <span class="is-size-6">({{provider.id}})</span>
            <div class="is-size-7">{{provider.totalAppointments}} appointments</div>
            <button class="button is-dark is-small is-rounded"
                    @click.prevent="showModal(provider)"
                    :disabled="!isDisabled(scheduleByProvider,provider.id)">Open Hours</button>
          </div>
        </div>
      </div>
      <div v-else>
        <h5 class="subtitle has-text-centered is-medium">Choose columns available for scheduling</h5>
        <div class="columns is-multiline">
          <div v-for="column in sortedColumns" :key="column.id" class="column is-4">
            <input type="checkbox" class="checkbox" :value="column" v-model="columnsUsed">
            {{column.name}} <span class="is-size-6">({{column.id}})</span>
            <div class="is-size-7">{{column.totalAppointments}} appointments</div>
            <button class="button is-dark is-small is-rounded"
                    @click.prevent="showModal(column)"
                    :disabled="!isDisabled(scheduleByProvider,column.id)">Open Hours</button>
          </div>
        </div>
      </div>
    </form>
    <ScheduleModal
      v-show="isModalVisible"
      @close="closeModal"
      :resource="selectedResource"
      ></ScheduleModal>
  </div>
</template>

<script>
import ScheduleModal from '@/components/scheduling/ScheduleModal'
export default {
  components: {
    ScheduleModal
  },
  props: {
    accountData: {
      type: Object,
      required: true
    },
    pimsData: {
      type: Object,
      required: true
    },
    scheduleOptions: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      software: this.accountData.software,
      scheduleByProvider: this.scheduleOptions.scheduleByProvider,
      officeHours: this.scheduleOptions.officeHours,
      columnsUsed: this.scheduleOptions.columnsUsed,
      providersUsed: this.scheduleOptions.providersUsed,
      pimsColumns: this.pimsData.columns,
      pimsProviders: this.pimsData.providers,
      isModalVisible: false,
      selectedResource: {}
    }
  },
  computed: {
    sortedProviders: function () {
      return this.pimsProviders.sort(
        (a, b) => b.totalAppointments - a.totalAppointments
      )
    },
    sortedColumns: function () {
      return this.pimsColumns.sort(
        (a, b) => b.totalAppointments - a.totalAppointments
      )
    }
  },
  methods: {
    isDisabled: function (byProvider, id) {
      if (byProvider) {
        if (
          this.providersUsed.filter(function (e) {
            return e.id === id
          }).length > 0
        ) {
          return true
        }
      } else {
        if (
          this.columnsUsed.filter(function (e) {
            return e.id === id
          }).length > 0
        ) {
          return true
        }
      }
    },
    showModal (resource) {
      this.isModalVisible = true
      this.selectedResource = resource
      console.log(this.selectedResource)
    },
    closeModal () {
      this.isModalVisible = false
    }
  }
}
</script>

<style scoped>
</style>
