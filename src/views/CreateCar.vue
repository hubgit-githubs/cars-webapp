<template>
  <div class="wrapper">
    <div class="section page-header header-filter" :style="headerStyle">
      <div class="container">
        <div class="md-layout">
          <div
            class="md-layout-item md-size-33 md-small-size-66 md-xsmall-size-100 md-medium-size-40 mx-auto"
          >
            <form @submit.prevent="save">
              <login-card header-color="info">
                <h4 slot="title" class="card-title">Nouvelle voiture</h4>
                <md-field class="md-form-group" slot="inputs">
                  <md-icon>label</md-icon>
                  <label>Nom</label>
                  <md-input v-model="name" required></md-input>
                </md-field>
                <md-field class="md-form-group" slot="inputs">
                  <md-icon>info</md-icon>
                  <label>Description</label>
                  <md-input v-model="description"></md-input>
                </md-field>
                <md-button type="submit" slot="footer" class="md-simple md-info md-lg">
                  Enregistrer
                </md-button>
              </login-card>
            </form>
          </div>
        </div>
      </div>
    </div>

    <notifications group="createCar" position="bottom right"/>
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
      name: '',
      description: ''
    };
  },
  props: {
    header: {
      type: String,
      default: require("@/assets/img/car3.jpg")
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
    checkUser(){
      if(!this.$session.exists('user')){
        this.$router.push({
          path: '/'
        });
      }
    },

    save(){
      var dataForm = {}
      dataForm['id'] = 0
      dataForm['name'] = this.name
      dataForm['description'] = this.description

      axios.post(this.$apiUrl + 'car', dataForm)
        .then(response => {
            // this.$router.push({
            //   path: '/'
            // });
            this.name = ''
            this.description = ''
            this.notify('Enregistrement réussi.', 'success')
        });
    },

    notify(text, type) {
      this.$notify({
          group: 'createCar',
          duration:5000,
          text: text,
          type: type,
      });
    },
  }
};
</script>

<style lang="css"></style>
