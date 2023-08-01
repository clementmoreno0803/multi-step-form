<template>
  <div class="container">
    <article>
      <header>
        <div class="progress">
          <div
            class="progress-step"
            :class="{ active: index === activeStep }"
            v-for="(step, index) in formSteps"
            :key="step + index"
          >
            <div class="progress-step-number">
              {{ index + 1 }}
            </div>
            <div class="progress-step-title">
              <h5>STEP {{ index + 1 }}</h5>
              <h4>{{ step.title }}</h4>
            </div>
          </div>
        </div>
      </header>
      <section>
        <h2>{{ formSteps[activeStep].title }}</h2>
        <h3>{{ formSteps[activeStep].subTitle }}</h3>
        <div class="input-fields">
          <div
            class="input-container"
            v-for="(field, index) in formSteps[activeStep].fields"
            :key="field + index"
          >
            <input
              type="text"
              :class="{ 'wrong-input': !field.valid }"
              v-model="field.value"
              required
            />
            <label class="input-label">{{ field.label }}</label>
          </div>
        </div>
        <div class="actions">

            <button
            v-if="activeStep > 0"
            @click="backStep"
            >
              Go Back
            </button>
          <button
            v-if="activeStep + 1 < formSteps.length - 1"
            @click="checkFields"
          >
            Next
          </button>
          <button
            v-if="activeStep + 1 === formSteps.length - 1"
            @click="checkFields"
          >
            Done
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
      animation: "animate-in",
      formSteps: [
        {
          title: "HTML Quiz",
          subTitle:
            "Please provide your name, email address and telephone number",
          fields: [
            {
              label: "What does HTML stand for?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
            {
              label: "Who is making the Web standards?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
            {
              label: "Element for the largest heading?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
          ],
        },
        {
          title: "CSS Quiz",
          fields: [
            {
              label: "What does CSS stand for?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
            {
              label: "HTML tag for an internal style sheet?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
            {
              label: "Property for the background color?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
          ],
        },
        {
          title: "Your data",
          fields: [
            {
              label: "Your first name?",
              value: "",
              valid: true,
              pattern: /.+/,
            },
            { label: "Your last name?", value: "", valid: true, pattern: /.+/ },
            {
              label: "Your email?",
              value: "",
              valid: true,
              pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
            },
          ],
        },
        {
          title: "Thank you for participating!",
        },
      ],
    };
  },
  methods: {
    nextStep() {
      this.activeStep += 1;
    },
    backStep(){
        this.activeStep -= 1;
    },
    checkFields() {
      let valid = true;
      console.log(this.step);
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
        this.animation = "animate-wrong";
        setTimeout(() => {
          this.animation = "";
        }, 400);
      }
    },
  },
};
</script>
  
  <style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css?family=Noto+Sans&display=swap");

@mixin flexbox() {
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  @include flexbox();
  width: 100%;
  min-height: 100vh;
  font-family: "Noto Sans", sans-serif;
  background: radial-gradient(#df5c2e, #a43227);
}

article {
  display: flex;
  margin: 10px;
  width: 100%;
  max-width: 720px;
  min-height: 480px;
  background: white;
  padding: 10px;
  border-radius: 15px;

  header {
    display: flex;
    justify-content: center;
    background-image: url("../assets/bg-sidebar-desktop.svg");
    width: 400px;
    background-size: cover;
    height: 580px;
    background-color: #fff;
    border-radius: 15px;
    padding-top: 20px;
  }

  .progress {
    padding: 20px;
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
        display: flex;
        justify-content: center;
        align-items: center;
        width: 30px;
        height: 30px;
        border-radius: 50%;
        color: #fff;
        border: 1px solid white;
        //   background-color: #df52e;
        font-weight: bold;
      }
      .progress-step-title {
        margin-left: 10px;
        color: white;

        h4 {
          margin: 0;
        }
        h5 {
          text-align: left;
          margin: 0;
          font-size: 0.775rem;
          opacity: 0.5;
        }
      }
    }
  }
  section {
    @include flexbox();
    flex-direction: column;
    width: 100%;
    background-color: #fff;

    h2 {
      font-size: 1.6rem;
      color: #df5c2e;
      margin: 0;
      padding: 20px;
    }

    .input-fields {
      @include flexbox();
      flex-direction: column;
      width: 100%;
    }

    .input-container {
      position: relative;
      padding: 30px 20px 20px 20px;
      width: calc(100% - 40px);
      max-width: 400px;

      input {
        position: relative;
        width: 100%;
        font-family: "Noto Sans", sans-serif;
        font-size: 1.35rem;
        outline: none;
        background: transparent;
        box-shadow: none;
        border: none;
        border-bottom: 2px dashed #df5c2e;

        &:valid + .input-label {
          top: 10px;
          left: 20px;
          font-size: 0.7rem;
          font-weight: normal;
          color: #999;
        }

        &.wrong-input + .input-label {
          color: #b92938;
        }
      }
    }

    .input-label {
      position: absolute;
      top: 32px;
      left: 20px;
      font-size: 1.35rem;
      pointer-events: none;
      transition: 0.2s ease-in-out;
    }

    .actions {
      margin: 0;

      button {
        font-family: "Noto Sans", sans-serif;
        outline: none;
        border: none;
        color: #fff;
        background-color: #df5c2e;
        font-size: 1.35rem;
        padding: 5px 20px;
        margin: 0;
        text-transform: uppercase;
        border-radius: 3px;
        cursor: pointer;
      }
    }
  }
}

.animate-in {
  transform-origin: left;
  animation: in 0.6s ease-in-out;
}

.animate-out {
  transform-origin: bottom left;
  animation: out 0.6s ease-in-out;
}

.animate-wrong {
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