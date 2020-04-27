<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-12 col-md-6">
        <div class="card my-5">
          <div class="card-header">
            <h3>Sign Up <small class="text-muted font-weight-light">(Password Strength Test - Nuxt.js)</small> </h3>
          </div>
          <div class="card-body">
            <form>
              <div class="form-group">
                <label for="email">Email</label>
                <input id="inputEmail" type="email" name="email" class="form-control" />
              </div>
              <div class="form-group">
                <label for="password">Password</label>
                <div class="input-group">
                  <input id="inputPassword"
                         v-model="input_password"
                         :type="passwordInputType"
                         name="password"
                         class="form-control"
                         @input="passwordCheck" />
                  <div class="input-group-append">
                    <button type="button" class="btn btn-link" @click.prevent="togglePasswordShow"> {{ passwordInputType === 'password' ? 'Hide' : 'Show' }} </button>
                  </div>
                </div>
              </div>
              <div class="d-flex flex-row justify-content-between">
                <div>
                  <button type="submit" class="btn btn-primary">Sign Up</button>
                </div>
                <div class="card">
                  <div class="card-body">
                    <h5>Password Requirements</h5>
                    <ul class="list-unstyled">
                      <li><i class="fal" :class="passwordStrength.length > 7 ? 'fa-check-circle' : 'fa-circle'"></i> Minimum 8 characters in length</li>
                      <li><i class="fal" :class="passwordStrength.uppercase && passwordStrength.lowercase ? 'fa-check-circle' : 'fa-circle'"></i> upper and lowercase letters</li>
                      <li><i class="fal" :class="passwordStrength.numbers && passwordStrength.symbols ? 'fa-check-circle' : 'fa-circle'"></i> number and symbol</li>
                    </ul>
                    <div class="progress">
                      <div class="progress-bar"
                           :class="strengthMeter"
                           role="progressbar"
                           :aria-valuenow="passwordStrength.eval.score"
                           aria-valuemin="0"
                           aria-valuemax="4">
                      </div>
                    </div>
                    <p class="text-center font-weight-bolder">
                      {{ passwordStrength.eval.title }}
                    </p>
                  </div>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import zxcvbn from 'zxcvbn'

export default {
  components: {},
  props: {},
  data() {
    return {
      input_password: '',
      passwordInputType: 'password',
      passwordStrength: {
        length: 0,
        uppercase: false,
        lowercase: false,
        numbers: false,
        symbols: false,
        eval: { score: 0, title: 'Weak' }
      }
    }
  },
  computed: {
    passwordState() {
      return this.passwordStrength
    },
    strengthMeter() {
      switch (this.passwordStrength.eval.score) {
        case 0:
          return 'str-0'
        case 1:
          return 'str-1'
        case 2:
          return 'str-2'
        case 3:
          return 'str-3'
        case 4:
          return 'str-4'
        default:
          return 'str-0'
      }
    }
  },
  methods: {
    togglePasswordShow() {
      this.passwordInputType = this.passwordInputType === 'password' ? 'text' : 'password'
    },
    strengthEval() {
      switch (zxcvbn(this.input_password).score) {
        case 0:
          return { score: 0, title: 'Weak' }
        case 1:
          return { score: 1, title: 'Weak' }
        case 2:
          return { score: 2, title: 'Fair' }
        case 3:
          return { score: 3, title: 'Good' }
        case 4:
          return { score: 4, title: 'Strong' }
        default:
          return { score: 0, title: 'Weak' }
      }
    },
    passwordCheck() {
      this.passwordStrength.length = this.input_password.length
      this.passwordStrength.uppercase = this.hasUpperCase()
      this.passwordStrength.lowercase = this.hasLowerCase()
      this.passwordStrength.symbols = this.hasSymbol()
      this.passwordStrength.numbers = this.hasNumber()
      this.passwordStrength.eval = this.strengthEval()
    },
    hasUpperCase() {
      let upper = false;
      [...this.input_password].forEach(c => {
        if (/^[A-Z]*$/.test(c)) {
          upper = true
        }
      })
      return upper
    },
    hasLowerCase() {
      let lower = false;
      [...this.input_password].forEach(c => {
        if (/^[a-z]*$/.test(c)) {
          lower = true
        }
      })
      return lower
    },
    hasSymbol() {
      let symbol = false;
      [...this.input_password].forEach(c => {
        if (/^[!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~]*$/.test(c)) {
          symbol = true
        }
      })
      return symbol
    },
    hasNumber() {
      let number = false;
      [...this.input_password].forEach(c => {
        if (/^[0-9]*$/.test(c)) {
          number = true
        }
      })
      return number
    }
  }
}
</script>

<style lang="scss" scoped>
  small {
    font-size: 50%;
  }
  .fa-check-circle {
    color: #4BBF73;
  }
</style>
