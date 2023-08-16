<template>
  <div class="container">
    <article>
      <header>
        <!-- Progress bar with numbers -->
        <div class="progress">
          <div
            class="progress-step"
            :class="{ active: index === activeStep }"
            v-for="(step, index) in formSteps"
            :key="step + index"
            @click=" console.log(activeStep = index) "
          >
            <div class="progress-step-number" >
              {{ index + 1 }}
              
            </div>
            <div class="progress-step-title">
              <h5>STEP {{ index + 1 }}</h5>
              <h4>{{ step.title }}</h4>
            </div>
          </div>
        </div>
      </header>

      <!-- content for the differents parts  -->
      <section>
        <div class="section-title">
          <h2 v-if="this.thankingStep == false">
            {{ formSteps[activeStep].title }}
          </h2>
          <h3 v-if="this.thankingStep == false">
            {{ formSteps[activeStep].subTitle }}
          </h3>
        </div>
        <div class="input-fields" v-if="activeStep == 0">
          <div
            class="input-container"
            v-for="(field, index) in formSteps[activeStep].fields"
            :key="field + index"
          >
            <input
              type="text"
              :class="{ 'wrong-input': !field.valid }"
              :placeholder="field.placeholder"
              v-model="field.value"
              required
            />
            <label class="input-label">{{ field.label }}</label>
          </div>
        </div>
        <!-- ENROLMENT PLAN PART -->
        <div class="input-fields" v-else-if="activeStep == 1">
          <div class="input-plan">
            <div class="input-plan-button">
              <button
                v-for="(item, index) in formSteps[activeStep].fields"
                :key="index"
                :class="{ active: item.active }"
                @click="selectPlan(item)"
              >
                <div v-html="item.image"></div>
                <div class="input-plan-button-information">
                  {{ item.label }}
                  <h4>
                    {{
                      planEnrollment
                        ? `$ ${item.value * 10}/yr`
                        : `$ ${item.value}/mo`
                    }}
                  </h4>
                  <span
                    class="input-plan-button-information-free"
                    v-if="planEnrollment"
                    >2 months free</span
                  >
                </div>
              </button>
            </div>
            <div class="plan-switch-button">
              <p>Monthly</p>
              <label class="switch" for="checkbox">
                <input type="checkbox" id="checkbox" v-model="planEnrollment" />
                <div class="slider round"></div>
              </label>
              <p>Yearly</p>
            </div>
          </div>
        </div>
        <!-- ADD ONS PART -->
        <div class="input-fields" v-else-if="activeStep == 2">
          <div
            class="input-addons"
            v-for="item in formSteps[activeStep].fields"
            :key="item.id"
            :class="{ active: item.isChecked }"
          >
            <input
              type="checkbox"
              v-model="item.isChecked"
              @click="addToTheSummary(item)"
            />
            <div class="input-addons-titles">
              <h3>
                {{ item.label }}
              </h3>
              <span>
                {{ item.description }}
              </span>
            </div>
            {{
              planEnrollment ? `+$${item.value * 10}/yr` : `+$${item.value}/mo`
            }}
          </div>
        </div>
        <!-- SUMMARY -->
        <div class="input-fields" v-else>
          <div v-if="this.thankingStep == false">
            <div class="summary">
              <div class="summary-plan">
                <div class="summary-plan-title">
                  <h4>
                    {{
                      planEnrollment
                        ? ` ${this.selectedPlan.label}(Yearly)`
                        : ` ${this.selectedPlan.label}(Monthly)`
                    }}
                  </h4>
                  <span style="text-decoration-line: underline;color: black;" @click=" activeStep = 1 ">Change</span>
                </div>
                <h4>
                  {{
                    planEnrollment
                      ? `$${this.selectedPlan.value * 10}/yr`
                      : `$${this.selectedPlan.value}/mo`
                  }}
                </h4>
              </div>
              <span class="summary-plan-border"></span>
              <div v-for="(sum, index) in summary" :key="index">
                <div class="summary-plan">
                  <h4 class="summary-plan-title">
                    {{ planEnrollment ? ` ${sum.label}` : ` ${sum.label}` }}
                  </h4>
                  <h4>
                    {{
                      planEnrollment
                        ? `+$ ${sum.value * 10}/yr`
                        : `+$ ${sum.value}/mo`
                    }}
                  </h4>
                </div>
              </div>
            </div>
            <div class="summary-total">
              <h4 class="summary-total-title">
                {{ planEnrollment ? "Total(Yearly)" : "Total(Monthly)" }}
              </h4>
              <h4>
                {{
                  planEnrollment
                    ? `${
                        this.selectedPlan.value * 10 + calculateTotal * 10
                      }$/yr`
                    : `${this.selectedPlan.value + calculateTotal}$/mo`
                }}
              </h4>
            </div>
          </div>
          <div v-else>
            <img src="../assets/icon-thank-you.svg" alt="" />
            <h3>Thank You</h3>
            <h4>
              Thanks for confirming your subscription! We hope you have fun
              using our platform. If you ever need support, please feel free to
              email us at support@loremgaming.com
            </h4>
          </div>
        </div>
        <!-- ACTIONS BUTTON -->
        <div class="actions">
          <button
            v-if="activeStep > 0 && this.thankingStep == false"
            @click="backStep"
            class="actions-back"
          >
            Go Back
          </button>
          <button
            v-if="
              activeStep + 1 < formSteps.length && this.thankingStep == false
            "
            @click="checkFields"
          >
            Next
          </button>
          <button
            v-if="
              activeStep + 1 == formSteps.length && this.thankingStep == false
            "
            @click="this.thankingStep = true"
          >
            Confirm
          </button>
        </div>
      </section>
    </article>
  </div>
</template>
  
  <script>
export default {
  data: () => {
    return {
      activeStep: 0,
      animation: "",
      planEnrollment: false,
      selectedPlan: null,
      thankingStep: false,
      summary: [],
      formSteps: [
        {
          progressTitle: "YOUR INFO",
          title: "Personal Info",
          subTitle:
            "Please provide your name, email address and telephone number",
          fields: [
            {
              label: "Name",
              value: "",
              valid: true,
              placeholder: "Insert your Name",
              pattern: /.+/,
            },
            {
              label: "Email Adress",
              value: "",
              valid: true,
              placeholder: "Insert your email adress : mail@email.com",
              pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
            },
            {
              label: "Phone Number",
              value: "",
              valid: true,
              placeholder: "Insert your phone number : 06 00 00 00 00",
              pattern: /^[+]?[(]?[0-9]{3}[)]?[-s.]?[0-9]{3}[-s.]?[0-9]{4,6}$/,
            },
          ],
        },
        {
          progressTitle: "SELECT PLAN",
          title: "Select plan",
          subTitle: "You have the option of monthly or yearly billing.",
          fields: [
            {
              image: `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 40 40"><g fill="none" fill-rule="evenodd"><circle cx="20" cy="20" r="20" fill="#FFAF7E"/><path fill="#FFF" fill-rule="nonzero" d="M24.995 18.005h-3.998v5.998h-2v-5.998H15a1 1 0 0 0-1 1V29a1 1 0 0 0 1 1h9.995a1 1 0 0 0 1-1v-9.995a1 1 0 0 0-1-1Zm-5.997 8.996h-2v-1.999h2v2Zm2-11.175a2.999 2.999 0 1 0-2 0v2.18h2v-2.18Z"/></g></svg>`,
              label: "Arcade",
              value: 9,
              valid: true,
              active: false,
              pattern: /.+/,
            },
            {
              image: `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 40 40"><g fill="none" fill-rule="evenodd"><circle cx="20" cy="20" r="20" fill="#F9818E"/><path fill="#FFF" fill-rule="nonzero" d="M25.057 15H14.944C12.212 15 10 17.03 10 19.885c0 2.857 2.212 4.936 4.944 4.936h10.113c2.733 0 4.943-2.08 4.943-4.936S27.79 15 25.057 15ZM17.5 20.388c0 .12-.108.237-.234.237h-1.552v1.569c0 .126-.138.217-.259.217H14.5c-.118 0-.213-.086-.213-.203v-1.583h-1.569c-.126 0-.217-.139-.217-.26v-.956c0-.117.086-.213.202-.213h1.584v-1.554c0-.125.082-.231.203-.231h.989c.12 0 .236.108.236.234v1.551h1.555c.125 0 .231.083.231.204v.988Zm5.347.393a.862.862 0 0 1-.869-.855c0-.472.39-.856.869-.856.481 0 .87.384.87.856 0 .471-.389.855-.87.855Zm1.9 1.866a.86.86 0 0 1-.87-.852.86.86 0 0 1 .87-.855c.48 0 .87.38.87.855a.86.86 0 0 1-.87.852Zm0-3.736a.862.862 0 0 1-.87-.854c0-.472.39-.856.87-.856s.87.384.87.856a.862.862 0 0 1-.87.854Zm1.899 1.87a.862.862 0 0 1-.868-.855c0-.472.389-.856.868-.856s.868.384.868.856a.862.862 0 0 1-.868.855Z"/></g></svg>`,
              label: "Advanced",
              value: 12,
              valid: true,
              active: false,
              pattern: /.+/,
            },
            {
              image: `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 40 40"><g fill="none" fill-rule="evenodd"><circle cx="20" cy="20" r="20" fill="#483EFF"/><path fill="#FFF" fill-rule="nonzero" d="M26.666 13H13.334A3.333 3.333 0 0 0 10 16.333v7.193a3.447 3.447 0 0 0 2.14 3.24c1.238.5 2.656.182 3.56-.8L18.52 23h2.96l2.82 2.966a3.2 3.2 0 0 0 3.56.8 3.447 3.447 0 0 0 2.14-3.24v-7.193A3.333 3.333 0 0 0 26.666 13Zm-9.333 6H16v1.333a.667.667 0 0 1-1.333 0V19h-1.333a.667.667 0 0 1 0-1.334h1.333v-1.333a.667.667 0 1 1 1.333 0v1.333h1.333a.667.667 0 1 1 0 1.334Zm7.333 2a2.667 2.667 0 1 1 0-5.333 2.667 2.667 0 0 1 0 5.333ZM26 18.333a1.333 1.333 0 1 1-2.667 0 1.333 1.333 0 0 1 2.667 0Z"/></g></svg>`,
              label: "Pro",
              value: 41,
              valid: true,
              active: false,
              pattern: /.+/,
            },
          ],
        },
        {
          progressTitle: "ADD-ONS",
          title: "Pick add-ons",
          subTitle: "Adds-on help you to enhance your gaming experience",

          fields: [
            {
              label: "Online service",
              description: "Access to multiplayer games",
              value: 1,
              valid: true,
              isChecked: false,
              pattern: /.+/,
            },
            {
              label: "Larger storage",
              description: "Extra 1TB of cloud save",
              value: 2,
              valid: true,
              isChecked: false,
              pattern: /.+/,
            },
            {
              label: "Customizable profil",
              description: "Customize theme on your profil",
              value: 2,
              valid: true,
              isChecked: false,
              pattern: /.+/,
            },
          ],
        },
        {
          progressTitle: "SUMMARY",
          title: "Finishing up",
          subTitle: "Adds-on help you to enhance your gaming experience",
        },
      ],
    };
  },
  methods: {
    nextStep() {
      this.activeStep += 1;
    },
    backStep() {
      this.activeStep -= 1;
    },
    // Vérifie si les valeurs rentrées respectent les regex
    // Check if the users input respecting the regex rules
    checkFields() {
      let valid = true;
      this.formSteps[this.activeStep].fields.forEach((field) => {
        if (!field.pattern.test(field.value)) {
          valid = false;
          field.valid = false;
        } else {
          field.valid = true;
        }
      });

      if (valid) {
        this.nextStep();
      } else {
        this.animation = "wrong-input";
        setTimeout(() => {
          this.animation = "";
        }, 400);
      }
    },
    // Permet de séléctionner un plan d'abonnement
    // Allow to select a plan
    selectPlan(item) {
      this.selectedPlan = item;
      this.formSteps[1].fields.forEach((i) => {
        if (i.label === item.label) {
          i.active = !i.active; // Activer/Désactiver le bouton cliqué
        } else {
          i.active = false; // Désactiver les autres boutons
        }
      });
    },
    // Rajoute la valeur de l'objet dans le tableau d'objet "summary"
    // Add the object value into the "summary" object array
    addToTheSummary(item) {
      if (!item.isChecked) {
        this.summary.push(item);
      } else {
        // Si la case est décochée, enlevez l'objet du tableau des objets sélectionnés
        // If the checkbox is unchecked, take off the object of the selected object array
        const index = this.summary.findIndex((t) => t.label == item.label);
        if (index !== -1) {
          this.summary.splice(index, 1);
        }
      }
    },
  },
  computed: {
    // Permet de faire l'addition de la valeur des options "add-ons"
    // Makes the sum up of "add-ons" options value
    calculateTotal() {
      return this.summary.reduce((total, sum) => total + sum.value, 0);
    },
  },
};
</script>
  
  <style lang="scss" scoped>
$white: #fff;
$light-blue: #edf3ff;
$red: fff;
@mixin flexbox($justify, $align) {
  display: flex;
  justify-content: $justify;
  align-items: $align;
}

span {
  font-size: 0.65rem;
  opacity: 0.5;
  font-weight: bold;
}
.container {
  @include flexbox(center, center);
  width: calc(100% - 40px);
  padding: 0 20px;
  min-height: 100vh;
  font-family: "Noto Sans", sans-serif;
  background: radial-gradient(#f0ffff, #e8fcff);
}

article {
  display: flex;
  margin: 10px;
  width: 100%;
  max-width: 1080px;
  min-height: 480px;
  background: $white;
  padding: 10px;
  border-radius: 15px;
  @media (max-width: 768px) {
    flex-direction: column;
    align-items: center;
    padding: 0;
    width: 100%;
  }

  header {
    display: flex;
    justify-content: center;
    background-image: url("../assets/bg-sidebar-desktop.svg");
    width: 400px;
    background-size: cover;
    height: 580px;
    background-color: $white;
    border-radius: 15px;
    padding-top: 20px;
    @media (max-width: 768px) {
      width: 100%;
      height: 150px;
      background-position-y: bottom;
      background-size: cover;
    }
  }

  .progress {
    padding: 20px;
    @media (max-width: 768px) {
      display: flex;
      gap: 20px;
    }
    .progress-step {
      display: flex;
      align-items: center;
      position: relative;
      margin-bottom: 20px;

      &.active {
        ~ .progress-step::before {
          background-color: #ccc;
        }
        .progress-step-number {
          color: #000000;
          background-color: #bdfbfa;
        }
      }
      .progress-step-number {
        @include flexbox(center, center);
        width: 30px;
        height: 30px;
        border-radius: 50%;
        color: $white;
        border: 1px solid $white;
        font-weight: bold;
      }
      .progress-step-title {
        margin-left: 10px;
        color: $white;

        h4 {
          margin: 0;
        }
        h5 {
          text-align: left;
          margin: 0;
          font-size: 0.775rem;
          opacity: 0.5;
        }
        @media (max-width: 768px) {
          display: none;
        }
      }
    }
  }
  section {
    @include flexbox(space-between, center);
    flex-direction: column;
    width: 100%;
    background-color: $white;
    padding: 30px;
    @media (max-width: 768px) {
      max-width: fit-content;
      width: calc(100% - 60px);
    }

    h2 {
      font-size: 1.6rem;
      color: #2a2928;
      margin: 0;
      padding: 10px 20px;
      @media (max-width: 768px) {
        padding: 0;
        margin: 10px 0;
      }
    }

    h3 {
      @media (max-width: 768px) {
        font-size: 0.7rem;
        margin-bottom: 30px;
      }
    }

    .input-fields {
      @include flexbox(center, center);
      flex-direction: column;
      width: 100%;
    }

    .input-container {
      position: relative;
      padding: 30px 20px 20px 20px;
      width: calc(100% - 40px);
      max-width: 400px;
      @media (max-width: 768px) {
        width: 100%;
      }

      input {
        position: relative;
        width: 100%;
        font-size: 0.9rem;
        padding: 5px;
        outline: none;
        background: transparent;
        box-shadow: none;
        border: 1px solid $light-blue;

        &::placeholder {
          font-size: 0.9rem;
          padding-left: 5px;
        }

        &.wrong-input + .input-label {
          color: #b92938;
        }
      }
    }

    .input-label {
      position: absolute;
      top: 10px;
      left: 20px;
      font-size: 0.9rem;
      pointer-events: none;
      transition: 0.2s ease-in-out;
    }
    .input-plan {
      padding: 10px;
      .input-plan-button {
        @include flexbox(space-between, center);
        gap: 50px;
        @media (max-width: 768px) {
          gap: 0;
          flex-direction: column;
        }
        button {
          width: 110px;
          height: 150px;
          display: flex;
          border-radius: 15px;
          flex-direction: column;
          justify-content: space-between;
          padding: 15px;
          margin: 30px 0;
          border: 1px solid #999;
          transition: all 0.4s ease-in-out;
          @media (max-width: 768px) {
            margin: 10px 0;
            width: 100%;
            flex-direction: row;
            height: fit-content;
            justify-content: flex-start;
          }

          &:hover {
            border: 1px solid rgb(68, 68, 140);
            cursor: pointer;
            transition: all 0.4s ease-out;
          }
          &.active {
            background: rgb(245, 251, 253);
            border: 1px solid rgb(68, 68, 140);
            transition: all 0.4s ease-out;
          }
          .input-plan-button-information {
            text-align: left;
            @media (max-width: 768px) {
            margin: 0 10px;
          }
          }
          .plan-image {
            width: 50px;
            height: 50px;
          }
        }
      }
    }
    .plan-switch-button {
      @include flexbox(center, center);
      gap: 20px;
      background: rgb(245, 251, 253);
      width: 100%;
      padding: 10px 0;
      border-radius: 5px;
      .switch {
        display: inline-block;
        height: 26px;
        position: relative;
        width: 50px;
      }

      .switch input {
        display: none;
      }

      p{
        @media (max-width: 768px) {
        font-size: 0.7rem;
        padding: 0 10px;
      }
      }

      .slider {
        background-color: #2d3748;
        bottom: 0;
        cursor: pointer;
        left: 0;
        position: absolute;
        right: 0;
        top: 0;
        transition: 0.4s;
        &:before {
          background-color: $white;
          bottom: 3px;
          content: "";
          height: 20px;
          left: 3px;
          position: absolute;
          transition: 0.4s;
          width: 20px;
        }
      }
    }

    input:checked + .slider {
      background-color: #66bb6a;
    }

    input:checked + .slider:before {
      transform: translateX(23px);
    }

    .slider.round {
      border-radius: 34px;
    }

    .slider.round:before {
      border-radius: 50%;
    }

    .input-addons {
      @include flexbox(space-between, center);
      min-width: 380px;
      border: 1px solid grey;
      padding: 20px 10px;
      margin: 10px 0;
      border-radius: 15px;
      border: 1px solid rgb(223, 223, 223);
      transition: all 0.4s ease-in-out;
      @media (max-width: 768px) {
        min-width: 280px;

        }

      &:hover {
        border: 1px solid rgb(68, 68, 140);
        cursor: pointer;
        transition: all 0.4s ease-out;
      }
      &.active {
        background: rgb(245, 251, 253);
        border: 1px solid rgb(68, 68, 140);
        transition: all 0.4s ease-out;
      }
      .input-addons-titles {
        @include flexbox(flex-start, flex-start);
        flex-direction: column;
        padding: 0 20px;
        h3 {
          margin: 0;
        }
      }
    }

    .summary {
      background: rgb(245, 251, 253);
      padding: 20px;
      min-width: 400px;
      border-radius: 20px;
      @media (max-width: 768px) {
        min-width: 280px;
        }
      .summary-plan {
        @include flexbox(space-between, center);
        padding-bottom: 20px;

        .summary-plan-title {
          text-align: left;
          font-size: 0.8rem;
          color: rgb(183, 183, 183);
        }
      }
      .summary-plan-border {
        display: block;
        background: rgb(218, 218, 218);
        padding: 1px;
        margin-bottom: 15px;
      }
    }
    .summary-total {
      @include flexbox(space-between, center);
      padding: 20px;
    }
    .actions {
      margin: 0;
      display: flex;
      justify-content: space-between;
      width: 100%;
      @media (max-width: 768px) {
        margin  : 15px 0;
        }

      button {
        outline: none;
        border: none;
        color: $light-blue;
        background-color: #2a1f59;
        font-size: 1rem;
        font-weight: bold;
        padding: 5px 20px;
        text-transform: capitalize;
        border-radius: 3px;
        cursor: pointer;
      }
      .actions-back {
        background: none;
        color: #bec3ce;
        @media (max-width: 768px) {
        padding : 0;
        }
      }
    }
  }
}

.wrong-input {
  animation: wrong 0.4s ease-in-out;
}

@keyframes wrong {
  0% {
    transform: translateX(0);
  }
  20% {
    transform: translateX(40px);
  }
  40% {
    transform: translateX(20px);
  }
  60% {
    transform: translateX(40px);
  }
  80% {
    transform: translateX(20px);
  }
  100% {
    transform: translateX(0);
  }
}
</style>