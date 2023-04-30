<template>
  <div class="container is-fluid mt-5">
    <div class="columns is-centered">
      <div class="column is-8">
        <h1 class="title">Welcome</h1>
        <p>
          Lorem ipsum, dolor sit amet consectetur adipisicing elit. Nemo quae
          rem ipsum praesentium, tenetur doloremque libero. Fugit, dolore
          possimus molestias cupiditate iste deserunt! Aut aliquid rem quas
          consequatur non iste.
        </p>
      </div>
      <div class="column is-4">
        <h1 class="title">Sign Up</h1>
        <!-- Sign up form -->
        <div class="field">
          <label class="label">Username</label>
          <div class="control has-icons-left">
            <input
              v-model="$v.username.$model"
              :class="{ 'is-danger': $v.username.$error }"
              class="input"
              type="text"
            />
            <span class="icon is-small is-left">
              <i class="fas fa-user"></i>
            </span>
          </div>
          <template v-if="$v.username.$error">
            <p class="help is-danger" v-if="!$v.username.required">
              This field is required
            </p>
            <p class="help is-danger" v-if="!$v.username.minLength">
              Username must be atlest 8 characters
            </p>
            <p class="help is-danger" v-if="!$v.username.maxLength">
              Username must not exceed 20 characters
            </p>
          </template>
        </div>

        <div class="field">
          <label class="label">Password</label>
          <div class="control has-icons-left has-icons-right">
            <input
              class="input"
              type="password"
              v-model="$v.password.$model"
              :class="{ 'is-danger': $v.password.$error }"
            />
            <span class="icon is-small is-left">
              <i class="fas fa-lock"></i>
            </span>
          </div>
          <template v-if="$v.password.$error">
            <p class="help is-danger" v-if="!$v.password.required">
              The password is required
            </p>
            <p class="help is-danger" v-if="!$v.password.minLength">
              The password must be atleast 8 characters
            </p>
            <p class="help is-danger" v-if="!$v.password.complex">
              The password is not complex (must contain uppercase, lowercase,
              number)
            </p>
          </template>
        </div>

        <div class="field">
          <label class="label">Confirm Password</label>
          <div class="control has-icons-left has-icons-right">
            <input
              class="input"
              type="password"
              v-model="$v.confirm_password.$model"
              :class="{ 'is-danger': $v.confirm_password.$error }"
            />
            <span class="icon is-small is-left">
              <i class="fas fa-lock"></i>
            </span>
          </div>
          <template v-if="$v.confirm_password.$error">
            <p class="help is-danger" v-if="!$v.confirm_password.sameAs">
              This confirm password is not the same as password
            </p>
          </template>
        </div>

        <div class="field">
          <label class="label">Email</label>
          <div class="control has-icons-left has-icons-right">
            <input
              v-model="$v.email.$model"
              :class="{ 'is-danger': $v.email.$error }"
              class="input"
              type="text"
            />
            <span class="icon is-small is-left">
              <i class="fas fa-envelope"></i>
            </span>
          </div>
          <template v-if="$v.email.$error">
            <p class="help is-danger" v-if="!$v.email.required">
              This field is required
            </p>
            <p class="help is-danger" v-if="!$v.email.email">Invalid Email</p>
          </template>
        </div>

        <div class="field">
          <label class="label">Mobile Number</label>
          <div class="control has-icons-left has-icons-right">
            <input
              v-model="$v.mobile.$model"
              :class="{ 'is-danger': $v.mobile.$error }"
              class="input"
              type="text"
            />
            <span class="icon is-small is-left">
              <i class="fas fa-mobile"></i>
            </span>
          </div>
          <template v-if="$v.mobile.$error">
            <p class="help is-danger" v-if="!$v.mobile.required">
              This field is required
            </p>
            <p class="help is-danger" v-if="!$v.mobile.mobile">
              Invalid Mobile Number
            </p>
          </template>
        </div>

        <div class="field">
          <label class="label">First Name</label>
          <div class="control has-icons-left has-icons-right">
            <input
              v-model="$v.first_name.$model"
              class="input"
              type="text"
              :class="{ 'is-danger': $v.first_name.$error }"
            />
          </div>
          <template v-if="$v.first_name.$error">
            <p class="help is-danger" v-if="!$v.first_name.maxLength">
              First name must not exceed 150 characters
            </p>
          </template>
        </div>

        <div class="field">
          <label class="label">Last Name</label>
          <div class="control has-icons-left has-icons-right">
            <input
              class="input"
              type="text"
              v-model="$v.last_name.$model"
              :class="{ 'is-danger': $v.first_name.$error }"
            />
          </div>
          <template v-if="$v.last_name.$error">
            <p class="help is-danger" v-if="!$v.last_name.maxLength">
              Last name must not exceed 150 characters
            </p>
          </template>
        </div>

        <button class="button is-primary is-fullwidth" @click="submit()">
          Sign Up
        </button>

        <p class="my-3">Already have an account? <a href="#">Login</a></p>
      </div>
    </div>
  </div>
</template>

<script>
import {
  required,
  email,
  minLength,
  maxLength,
  sameAs
} from "vuelidate/lib/validators";
import axios from "axios";

function mobile(value) {
  return !!value.match(/0[0-9]{9}/);
}

function complexPassword(value) {
  if (!(value.match(/[a-z]/) && value.match(/[A-Z]/) && value.match(/[0-9]/))) {
    console.log("it isn't complex");
    return false;
  }
  return true;
}

export default {
  data() {
    return {
      username: "earthsupakorn",
      password: "Ea123Ea123Ea123",
      confirm_password: "Ea123Ea123Ea123",
      email: "supakorn@gmail.com",
      mobile: "0959259515",
      first_name: "supakorn",
      last_name: "netsuwan"
    };
  },
  methods: {
    submit() {
      // Validate all fields
      this.$v.$touch();

      // เช็คว่าในฟอร์มไม่มี error
      if (!this.$v.$invalid) {
        let data = {
          username: this.username,
          password: this.password,
          confirm_password: this.confirm_password,
          email: this.email,
          mobile: this.mobile,
          first_name: this.first_name,
          last_name: this.last_name
        };

        axios
          .post("http://localhost:3000/user/signup", data)
          .then(res => {
            alert("Sign up Success");
          })
          .catch(err => {
            console.log(err.response)
            alert(err.response.data.details.message);
          });
      }
    }
  },
  validations: {
    username: {
      required,
      minLength: minLength(5),
      maxLength: maxLength(20)
    },
    first_name: {
      maxLength: maxLength(150)
    },
    last_name: {
      maxLength: maxLength(150)
    },
    email: {
      required,
      email
    },
    mobile: {
      required: required,
      mobile: mobile
    },
    password: {
      required: required,
      minLength: minLength(8),
      complex: complexPassword
    },
    confirm_password: {
      sameAs: sameAs("password")
    }
  }
};
</script>
