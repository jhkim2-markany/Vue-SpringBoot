<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="register-form">
        <div class="logo-wrapper">
          <img class="logo" src="/static/images/logo.png">
          <div class="tagline">Open source task management tool</div>
        </div>
        <form @submit.prevent="submitForm">
          <div v-show="errorMessage" class="alert alert-danger failed">{{ errorMessage }}</div>
          <div class="form-group">
            <label for="username">Username</label>
            <input type="text" class="form-control" id="username" v-model="form.username">
            <div class="field-error" v-if="$v.form.username.$dirty">
              <div class="error" v-if="!$v.form.username.required">Username is required</div>
              <div class="error" v-if="!$v.form.username.alphaNum">Username can only contain letters and numbers</div>
              <div class="error" v-if="!$v.form.username.minLength">Username must have at least {{$v.form.username.$params.minLength.min}} letters.</div>
              <div class="error" v-if="!$v.form.username.maxLength">Username is too long. It can contains maximium {{$v.form.username.$params.maxLength.max}} letters.</div>
            </div>
          </div>
          <div class="form-group">
            <label for="emailAddress">Email address</label>
            <input type="email" class="form-control" id="emailAddress" v-model="form.emailAddress">
            <div class="field-error" v-if="$v.form.emailAddress.$dirty">
              <div class="error" v-if="!$v.form.emailAddress.required">Email address is required</div>
              <div class="error" v-if="!$v.form.emailAddress.email">This is not a valid email address</div>
              <div class="error" v-if="!$v.form.emailAddress.maxLength">Email address is too long. It can contains maximium {{$v.form.emailAddress.$params.maxLength.max}} letters.</div>
            </div>
          </div>
          <div class="form-group">
            <label for="password">Password</label>
            <input type="password" class="form-control" id="password" v-model="form.password">
            <div class="field-error" v-if="$v.form.password.$dirty">
              <div class="error" v-if="!$v.form.password.required">Password is required</div>
              <div class="error" v-if="!$v.form.password.minLength">Password is too short. It can contains at least {{$v.form.password.$params.minLength.min}} letters.</div>
              <div class="error" v-if="!$v.form.password.maxLength">Password is too long. It can contains maximium {{$v.form.password.$params.maxLength.max}} letters.</div>
            </div>
          </div>
          <button type="submit" class="btn btn-primary btn-block">Create account</button>
          <p class="accept-terms text-muted">By clicking “Create account”, you agree to our <a href="#">terms of service</a> and <a href="#">privacy policy</a>.</p>
          <p class="text-center text-muted">Already have an account? <a href="/login">Sign in</a></p>
        </form>
      </div>
    </div>
    <footer class="footer">
      <span class="copyright">&copy; 2018 TaskAgile.com</span>
      <ul class="footer-links list-inline float-right">
        <li class="list-inline-item"><a href="#">About</a></li>
        <li class="list-inline-item"><a href="#">Terms of Service</a></li>
        <li class="list-inline-item"><a href="#">Privacy Policy</a></li>
        <li class="list-inline-item"><a href="https://github.com/taskagile/vuejs.spring-boot.mysql" target="_blank">GitHub</a></li>
      </ul>
    </footer>
  </div>
</template>

<script>
import registrationService from '@/service/registration'
import { required, email, minLength, maxLength, alphaNum } from 'vuelidate/lib/validators'
// 프로미스 쓸꺼임

export default {
  name: 'RegisterPage',
  data: function () {
    return {
      form: {
        username: '',
        emailAddress: '',
        password: ''
      },
      errorMessage: ''
    }
  },
  validations:{ //데이터 모델과 같은 구조를 갖는 옵션 객체를 추가함!
      form: {
          username:{
              required,
              minLength: minLength(2),
              maxLength: maxLength(50),
              alphaNum
          },
          emailAddress:{
              required,
              email,
              maxLength: maxLength(100)
          },
          password:{
              required,
              minLength: minLength(6),
              maxLength: maxLength(30)
          }
      }
  },
  methods: {
    submitForm () {
      this.$v.$touch() // Vue인스턴스에 추가한 $v 객체로 Veulidate API에 접근한다.
      // $v 객체는 검증에 대한 현재 상태를 가진다. $touch 메소드를 호출해 데이터 검증을 한다.
      if(this.$v.$invalid){ // invalid 속성으로 결과를 확인한다. 검증이 실패하면 true이며 회원가입이 중단된다.
        return
      }
      // TODO : 데이터 검증하기
      registrationService.register(this.form).then(() => {
        this.$router.push({name: 'LoginPage'})
      }).catch((error) => {
        this.errorMessage = 'Failed to register user. Reason: ' + (error.message ? error.message : 'Unknown') + '.'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  max-width: 900px;
}
.register-form {
  max-width: 320px;
  margin-top: 50px;
}
.logo-wrapper {
  text-align: center;
  margin-bottom: 40px;
  .tagline {
    line-height: 180%;
    color: #666;
  }
 .logo {
    max-width: 150px;
    margin: 0 auto;
  }
}
.register-form {
  .form-group label {
    font-weight: bold;
    color: #555;
  }
  .accept-terms {
    margin: 20px 0 40px 0;
  }
}
.footer {
  width: 100%;
  font-size: 13px;
  color: #666;
  line-height: 40px;
  border-top: 1px solid #ddd;
  margin-top: 50px;
  .list-inline-item {
    margin-right: 10px;
  }
  a {
    color: #666;
  }
}
</style>
