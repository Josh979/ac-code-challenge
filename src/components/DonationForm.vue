<template>
  <div class="donation-widget">
    <div class="top-0 my-4 mx-5">
      <div class="tooltip-wrapper" :class="goalMet ? 'goal-met' : ''">
        <div class="tooltip">
          <span class="text" v-if="goalMet">
            <strong>🚀 Goal Reached</strong> - ${{ addCommasToNumber(donations.current / 100) }} donated!
          </span>
          <span class="text" v-else>
              ${{ addCommasToNumber(amountRemaining) }} still needed to fund this project
          </span>
        </div>
        <div class="marker" :class="goalMet ? 'goal-met' : ''" :style="`left:${markerPosition}%;)`"></div>
      </div>
      <main class="card">
        <div class="progress">
          <div class="banner" :class="goalMet ? 'goal-met' : ''">Project Funded</div>
          <div class="progress-amount" :class="goalMet ? 'goal-met' : ''"
               :style="`width:${progressPercentage}%;`"></div>
        </div>

        <div class="wrapper">

          <h3 class="heading">
            Only four days left to fund this project
          </h3>
          <div class="content">
            Join the <span class="content-supporters">{{ donations.donors }}</span> other
            donor{{ donations.donors === 1 ? '' : 's' }} who {{ donations.donors === 1 ? 'has' : 'have' }} already
            supported
            the project.
          </div>
          <form @submit.prevent="addDonation()" class="form-group" :class="hasError ? 'error' : ''">
            <div class="error-label">{{ error.message }}</div>
            <transition name="fade" mode="out-in">
              <div v-show="showThankYouMessage && !hasError" class="success-ribbon">Donation Received - Thank you!</div>
            </transition>
            <div class="amount">
              <div class="prefix">$</div>
              <input class="input" @focusout="removeError" @keyup="donations.amount.length ? '' : removeError()"
                     v-model="donations.amount" type="text" placeholder="25">
            </div>
            <button type="submit" class="submit-button">Give Now</button>
          </form>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DonationForm',
  data() {
    return {
      donations: {
        goal: 500000,
        amount: '',
        minimumAmount: 5,
        current: 0,
        donors: 0
      },
      error: {
        active: false,
        message: ''
      },
      showThankYouMessage: false
    }
  },
  computed: {
    amountRemaining() {
      return (this.donations.goal - this.donations.current) / 100;
    },
    goalMet() {
      return this.donations.current >= this.donations.goal;
    },
    hasError() {
      return this.error.active;
    },
    percentage() {
      return Math.floor(parseInt(this.donations.current) / this.donations.goal * 100);
    },
    progressPercentage() {
      if (this.percentage <= 2.5) {
        return 2.5;
      } else if (this.percentage >= 97.5) {
        return 97.5;
      }
      return this.percentage;
    },
    markerPosition() {
      if (this.percentage <= 2.5) {
        return 2.5;
      } else if (this.percentage >= 97.5) {
        return 97.5;
      }
      return this.percentage;
    }
  },
  methods: {
    addDonation() {
      if (this.checkDonationAmount()) {
        this.donations.current += (this.donations.amount * 100);
        this.donations.amount = '';
        ++this.donations.donors;
        this.triggerTYMessage();
      }
    },
    checkDonationAmount() {
      // check if empty
      if (this.donations.amount.length === 0) {
        this.setErrorMessage('Please enter a donation amount.')
        this.setError();
        return false;
      }
      // check if above minimum donation level
      if (parseInt(this.donations.amount) < this.donations.minimumAmount) {
        this.setErrorMessage(`Minimum donation amount is $${this.donations.minimumAmount}.`)
        this.setError();
        return false;
      }
      // check if valid number
      const re = new RegExp(/^[\d]+$|^[\d]+[.][\d]{2}$/i);
      if (isNaN(this.donations.amount) || !re.test(this.donations.amount)) {
        this.setErrorMessage('Please enter a valid donation amount.')
        this.setError();
        return false;
      }
      this.removeError();
      return true;
    },
    setError() {
      this.error.active = true;
      this.showThankYouMessage = false;
    },
    setErrorMessage(msg) {
      this.error.message = msg;
    },
    removeError(e) {
      if (!Object.prototype.isPrototypeOf.call(e, 'key') || e.key !== 'Enter') {
        this.error.active = false;
      }
    },
    triggerTYMessage() {
      this.showThankYouMessage = true;
    },
    addCommasToNumber(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    }
  }
}
</script>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@400;500;700&family=Poppins:wght@700&display=swap');

$acBlue: #3459df;
$black: #1c1c1c;
$darkGray: #46495D;
$progressUpdateTransition: 250ms all ease-in-out;
$errorRed: #e00000;
$successGreen: darkgreen;

.donation-widget {
  display: inline-block;
  .tooltip-wrapper {
    position: relative;
    opacity: 1;
    border-radius: 4px;
    background: $darkGray;
    margin-bottom: 14px;

    &.goal-met {
      background: green;
    }

    .tooltip {
      height: 100%;
      display: flex;
      width: 100%;

      .text {
        color: white;
        font-weight: 400;
        font-size: .875rem;
        line-height: 1.75rem;
        padding-left: 9px;
      }
    }

    .marker {
      content: '';
      position: absolute;
      bottom: 0;
      left: 97%;
      width: 0;
      height: 0;
      border: 9px solid transparent;
      border-top-color: $darkGray;
      border-bottom: 0;
      margin-left: -9px;
      margin-bottom: -7px;
      transition: $progressUpdateTransition;

      &.goal-met {
        border-top-color: green;
      }
    }
  }

  main.card {
    //opacity: 0.5;
    width: 425px; /* width 423px with 2px of border */
    max-width: 100%;
    max-height: 500px;
    box-sizing: border-box;
    border: 1px solid #cbcfd3;
    border-radius: 8px;
    overflow: hidden;

    .heading {
      @apply text-left;
      font-size: 2rem;
      margin: {
        top: 7px;
        bottom: 7px;
      }
      line-height: 2.35rem;
      color: $black;
      font-weight: 700;
      font-family: Poppins, Helvetica, Arial, sans-serif;
    }

    .progress {
      height: 3px;
      width: 100%;
      overflow: hidden;
      position: relative;

      .banner {
        position: absolute;
        top: 0;
        background: green;
        color: white;
        padding: 3px;
        font-size: .8rem;
        text-align: center;
        width: 100%;

        &:not(.goal-met) {
          display: none;
        }

      }

      &-amount {
        width: 100%;
        height: 100%;
        background-color: #3859cf;
        transition: $progressUpdateTransition;

        &.goal-met {
          background-color: darkgreen;
        }
      }
    }

    .wrapper {
      display: flex;
      justify-content: stretch;
      flex-direction: column;
      position: relative;
      @apply p-7;

      .content {
        margin: 20px 0;
        line-height: 1.28rem;
        color: $darkGray;

        &-supporters {
          font-weight: bold;
        }
      }

      .success-ribbon {
        position: absolute;
        height: 25px;
        width: 100%;
        color: $successGreen;
        font-size: 0.75rem;
        margin-top: -20px;
      }

      .form-group {
        margin-top: 10px;
        margin-bottom: 5px;
        display: flex;
        justify-content: space-between;
        position: relative;

        .error-label {
          display: none;
        }

        &.error {
          .error-label {
            position: absolute;
            margin-top: -20px;
            left: 0;
            display: block;
            font-size: 0.75rem;
            color: $errorRed;
          }

          .amount {
            border-color: $errorRed;

            .input {
              color: $errorRed;
            }
          }
        }

        .amount {
          position: relative;
          max-width: 250px;
          border-radius: 4px;
          border: 1px solid #cbcfd3;
          height: 100%;
          width: 100%;
          overflow: hidden;

          .prefix {
            display: inline-block;
            text-align: right;
            height: 100%;
            font-size: 1rem;
            font-weight: 700;
            color: $black;
            width: 10%;
          }

          .input {
            width: 90%;
            display: inline-block;
            background: transparent;
            align-content: center;
            padding: 13px 5px;
            height: 100%;

            &:focus {
              outline: none;
            }
          }
        }

        .submit-button {
          color: white;
          font-weight: 500;
          font-size: 1rem;
          padding: 0 1.25rem;
          border-radius: 4px;
          background-color: $acBlue;
          transition: 125ms background-color;
          box-shadow: 0 0 0 0 rgba($acBlue, .35);
          -webkit-animation: 1s pulse 1.5s forwards;

          &:hover {
            background-color: darken($acBlue, 8);
            //-webkit-animation: none;
          }

          &:active {
            background-color: $acBlue;
          }
        }
      }
    }
  }

}


// safely support device widths down to 315px
@media (max-width: 450px) {
  .donation-widget {
    .tooltip-wrapper {
      margin-bottom: 10px;

      .marker {
        display: none;
      }
    }

    main.card {
      width: 100%;
      min-width: 275px;
      max-height: 100%;

      .wrapper {
        @apply p-4;

        h3.heading {
          margin: 0;
          font-size: 1.5rem;
          line-height: 1.75rem;
        }

        .content {
          margin: 1.25rem 0;
        }

        .form-group {
          margin-top: 5px;
          flex-direction: column;

          .amount {
            max-width: 100%;
            @apply mt-0 mb-2;
          }

          .submit-button {
            @apply my-2;
            padding: 1rem;
          }
        }
      }
    }
  }
}

@-webkit-keyframes pulse {
  0% {
    transform: scale(.9);
  }
  70% {
    transform: scale(1);
    box-shadow: 0 0 0 50px rgba($acBlue, 0);
  }
  100% {
    transform: scale(1);
    box-shadow: 0 0 0 0 rgba($acBlue, 0);
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 250ms;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
