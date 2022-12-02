<script>
import axios from "axios";

export default {
  name: "SubscribeForUpdates",
  props: {
    donationCampaignId: {
      type: String,
      required: true,
    },
    emailLabel: {
      type: String,
      default: "E-naslov",
    },
    emailPlaceholder: {
      type: String,
      default: "",
    },
    checkboxLabel: {
      type: String,
      default: "Strinjam se, da mi občasno pošljete elektronsko sporočilo.",
    },
    buttonText: {
      type: String,
      default: "Naroči me!",
    },
    buttonLoadingText: {
      type: String,
      default: "Nalaganje...",
    },
    onSubscriptionSuccess: {
      type: String,
      default:
        "Na tvoj spletni naslov smo poslali navodila, kako potrdiš naročnino. ",
    },
    onSubscriptionError: {
      type: String,
      default: "Napaka :(",
    },
  },
  data() {
    return {
      email: "",
      checkbox: false,
      checkboxError: false,
      loading: false,
      error: false,
      success: false,
    };
  },
  methods: {
    async onSubscribeClick() {
      this.loading = true; // to display loading message and disable the button

      // check if checkbox is checked
      if (!this.checkbox) {
        console.log("Ustavi se")
        this.checkboxError = true;
      } else {
        // send subscribe request
        try {
          const response = await axios.post(
            "https://podpri.lb.djnd.si/api/subscribe/",
            {
              email: this.email,
              campaign_id: this.donationCampaignId,
            }
          );

          if (response.data?.msg === "mail sent") {
            // on success show success message
            this.success = true;
          } else {
            // on error show error message
            this.error = true;
          }
        } catch (err) {
          // on error show error message
          this.error = true;
          // console.log(err.message)
        }
      }

      this.loading = false;
    },
  },
};
</script>

<template>
  <form @submit.prevent="onSubscribeClick" class="subscribe">
    <label for="emailInput" class="email-label">{{ emailLabel }}</label>
    <div class="email-wrapper">
      <input
        v-model="email"
        id="emailInput"
        :placeholder="emailPlaceholder"
        type="email"
        class="custom-control-input-text"
        required
      />
      <!-- TODO: if we want the button to be next to the input field
      <button ref="subscribeButton" class="btn" type="submit">
        {{ buttonText }}
      </button> -->
    </div>
    <label
      for="emailCheck"
      class="checkbox-wrapper"
      :class="{ error: checkboxError }"
    >
      <input
        v-model="checkbox"
        id="emailCheck"
        type="checkbox"
        class="custom-control-input-checkbox"
        @change="(checkboxError = false)"
      />
      <span class="custom-checkbox"></span>
      {{ checkboxLabel }}
    </label>
    <button class="btn" type="submit" :disabled="loading">
      <span v-if="loading">
        {{ buttonLoadingText }}
      </span>
      <span v-else>
        {{ buttonText }}
      </span>
    </button>
    <div :class="['email-confirm', { visible: success }]">
      {{ onSubscriptionSuccess }}
    </div>
    <div :class="['email-confirm', { visible: error }]">
      {{ onSubscriptionError }}
    </div>
  </form>
</template>

<style lang="scss" scoped>
form {
  position: relative;
  font-family: sans-serif;

  .email-label {
    display: block;
    padding-bottom: 8px;
    font-weight: bold;
  }

  .email-wrapper {
    position: relative;
  }

  .custom-control-input-text {
    border-radius: 0;
    border: 2px solid black;
    padding: 4px 8px;
    font-size: 16px;
  }

  .checkbox-wrapper {
    margin-top: 10px;
    margin-bottom: 10px;
    padding-left: 30px;
    min-height: 30px;
    display: flex;
    align-items: center;
    cursor: pointer;

    .custom-control-input-checkbox {
      position: absolute;
      visibility: hidden;
    }

    .custom-checkbox {
      position: absolute;
      display: flex;
      justify-content: center;
      align-items: center;
      left: 0;
      height: 20px;
      width: 20px;
      border: 2px black solid;
      &:after {
        content: " ";
        position: absolute;
        height: 80%;
        width: 80%;
        background-image: url("data:image/svg+xml;charset=utf8,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 8 8'%3E%3Cpath fill='%23291749' d='M6.564.75l-3.59 3.612-1.538-1.55L0 4.26 2.974 7.25 8 2.193z'/%3E%3C/svg%3E");
        background-size: contain;
        background-repeat: no-repeat;
        background-position: center;
        opacity: 0;
      }
    }

    .custom-control-input-checkbox:checked + .custom-checkbox {
      &:after {
        opacity: 1;
      }
    }

    &.error {
      color: red;

      .custom-checkbox {
        border-color: red;
      }
    }
  }
}

.btn {
  &,
  &:hover:enabled,
  &:active {
    padding: 8px 42px;
    font-size: 16px;
    font-weight: bold;
  }

  &:hover:not([disabled]) {
    cursor: pointer;
  }
}

.email-confirm {
  position: absolute;
  inset: 0;
  padding: 32px;
  display: none;
  align-items: center;
  justify-content: center;
  background-color: #ffffff;
  text-align: center;
  font-size: 24px;
  font-weight: bold;
  line-height: 1.2;

  &.visible {
    display: flex;
  }
}
</style>
