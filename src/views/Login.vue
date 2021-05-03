<template>
  <div class="wrapper">
    <div class="section page-header header-filter" :style="headerStyle">
      <div class="container">
        <div class="md-layout">
          <div
            class="md-layout-item md-size-33 md-small-size-66 md-xsmall-size-100 md-medium-size-40 mx-auto"
          >
            <form @submit.prevent="login">
              <login-card header-color="green">
                <h4 slot="title" class="card-title">Login</h4>
                <md-field class="md-form-group" slot="inputs">
                  <md-icon>email</md-icon>
                  <label>Email</label>
                  <md-input v-model="email" type="email" required></md-input>
                </md-field>
                <md-field class="md-form-group" slot="inputs">
                  <md-icon>lock_outline</md-icon>
                  <label>Mot de passe</label>
                  <md-input type="password" v-model="password" required></md-input>
                </md-field>
                <md-button type="submit" slot="footer" class="md-simple md-success md-lg">
                  Se connecter
                </md-button>
              </login-card>
            </form>
          </div>
        </div>
      </div>
    </div>

    <notifications group="login" position="bottom right"/>
  </div>
</template>

<script>
import { LoginCard } from "@/components";

export default {
  components: {
    LoginCard
  },
  bodyClass: "login-page",
  data() {
    return {
      email: '',
      password: '',

      dataForm:{},
    };
  },
  props: {
    header: {
      type: String,
      default: require("@/assets/img/profile_city.jpg")
    }
  },
  computed: {
    headerStyle() {
      return {
        backgroundImage: `url(${this.header})`
      };
    }
  },

  created(){
    this.checkUser()
  },

  methods: {
    login() {
      this.dataForm['name'] = this.name
      this.dataForm['email'] = this.email
      this.dataForm['password'] = this.password

      axios.post(this.$apiUrl + 'userCarLogin', this.dataForm)
        .then(response => {
            if(response.data.message == 'success'){
              this.$session.set('user', response.data.user)
              this.$router.push({
                  path: '/'
              });
            }else{
              this.$session.remove('user')
              this.notify('Vos identifiants sont incorrects', 'error')
            }
        });
    },

    checkUser(){
      if(this.$session.exists('user')){
        this.$router.push({
          path: '/'
        });
      }
    },

    notify(text, type) {
      this.$notify({
          group: 'login',
          duration:5000,
          text: text,
          type: type,
      });
    },
  }
};
</script>

<style lang="css"></style>
