<template>
  <div class="container">
    <div class="spacer"></div>
    <h2 class="subtitle is-3 has-text-centered">Account Setup</h2>
    <nav class="level">
      <div class="level-item has-text-centered" v-for="(title,index) in titles" :key="index">
        <button class="button is-fullwidth nav-button" :class="{'is-info': index === currentStepNumber - 1}">
          {{title}}
        </button>
      </div>
    </nav>
    <section class="section">
      <keep-alive>
        <transition name="slide-in">
          <component
            ref="currentStep"
            :is="currentStep"
            :wizard-data="account">
          </component>
        </transition>
      </keep-alive>
    </section>
    <div class="buttons is-right">
      <button
        @click="goBack"
        v-if="currentStepNumber > 1"
        class="button is-info"
      >Back
      </button>
      <button
        @click="goNext"
        :disabled="!canGoNext"
        class="button is-info"
      >{{isLastStep ? 'Submit' : 'Next'}}</button>
    </div>
    <div class="spacer"></div>
  </div>
</template>

<script>
import AccountHome from '@/components/account/AccountHome'
import AccountSettings from '@/components/account/AccountSettings'
import AccountSocial from '@/components/account/AccountSocial'
import AccountUser from '@/components/account/AccountUser'
import AccountSummary from '@/components/account/AccountSummary'

export default {
  components: {
    AccountHome,
    AccountSettings,
    AccountSocial,
    AccountUser,
    AccountSummary
  },
  data () {
    return {
      currentStepNumber: 1,
      canGoNext: true,
      asyncState: null,
      steps: [
        'AccountHome',
        'AccountSettings',
        'AccountSocial',
        'AccountUser',
        'AccountSummary'
      ],
      titles: [
        'Getting Started',
        'Account Settings',
        'Social Media',
        'User Setup',
        'Summary'
      ],
      account: {
        id: null,
        market: null,
        software: null,
        name: null,
        smsName: null,
        notificationEmail: null,
        phone: null,
        address: {
          line1: null,
          line2: null,
          city: null,
          state: null,
          postCode: null,
          timeZone: null
        },
        socialMedia: {
          website: null,
          facebook: null,
          twitter: null,
          instagram: null,
          googlePlus: null,
          yelp: null
        },
        users: {
          officeManager: {},
          accountant: {},
          providers: [],
          frontOffice: []
        }
      }
    }
  },
  computed: {
    isLastStep () {
      return this.currentStepNumber === this.length
    },
    inProgress () {
      return this.currentStepNumber <= this.length
    },
    length () {
      return this.steps.length
    },
    currentStep () {
      return this.steps[this.currentStepNumber - 1]
    },
    progress () {
      return (this.currentStepNumber / this.length) * 100
    }
  },
  methods: {
    goBack () {
      this.currentStepNumber--
      this.canGoNext = true
    },
    goNext () {
      this.currentStepNumber++
      this.$nextTick(() => {
        this.canGoNext = true
      })
    }
  },
  created () {
    this.$store.dispatch('getAccountData')
      .then(this.account = this.$store.accountData)
  }
}
</script>

<style scoped>
</style>
