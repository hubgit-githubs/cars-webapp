<template>
  <div class="wrapper">
    <div id="nav-tabs">
      <h3>Liste des voitures</h3>
      <div class="md-layout">

        <div v-for="car in cars" :key="car.id" class="md-layout-item md-size-50 md-small-size-100">
          <nav-tabs-card no-label>          
            <template slot="content">
              <md-tabs class="md-info" md-alignment="left" :md-active-tab="tab_activated">
                <md-tab id="tab-name" :md-label="car.name" md-disabled></md-tab>

                <md-tab v-if="logged" id="tab-comment" md-label="" md-icon="comment" md-active>
                  <p v-for="comment in car.comment" :key="comment.id" style="text-align:initial">
                    
                    <span style="color:#00bcd4" v-if="comment.user.id == user.id">
                      <md-icon style="color:#00bcd4">face</md-icon>
                      <b> Vous</b>
                    </span>

                    <span v-else>
                      <md-icon>face</md-icon>
                      <b> {{comment.user.name}}</b>
                    </span>
                    <br>

                    <span style="margin-left:15px">{{comment.comment}}</span>
                  </p>

                  <form @submit.prevent="saveComment">
                    <md-field class="md-form-group">
                      <md-icon>face</md-icon>
                      <md-input
                        v-model="userComment[car.id]"
                        placeholder="Votre commentaire"
                        required
                        autocomplete="off"
                      ></md-input>

                      <md-button type="submit" class="md-primary md-just-icon md-round" @click="car_id = car.id">
                        <md-icon>send</md-icon>
                      </md-button>
                    </md-field>
                  </form>
                </md-tab>

                <md-tab id="tab-description" md-label="" md-icon="info">
                  <p>{{car.description}}</p>
                </md-tab>

                
                <md-tab v-if="logged" id="tab-modification" md-label="" md-icon="edit">
                  <form @submit.prevent="saveCar">
                    <md-field class="md-form-group">
                      <label>Nom</label>
                      <md-icon></md-icon>
                      <md-input
                        v-model="carName[car.id]"
                        placeholder="Nom"
                        required
                      ></md-input>
                    </md-field>

                    <md-field class="md-form-group">
                      <label>Description</label>
                      <md-icon></md-icon>
                      <md-input
                        v-model="carDescription[car.id]"
                        placeholder="Description"
                      ></md-input>
                    </md-field>

                    <md-button type="submit" class="md-primary" md-alignment="right" @click="car_id = car.id">
                      <md-icon>save</md-icon>
                    </md-button>
                  </form>
                </md-tab>
                
              </md-tabs>
            </template>
          </nav-tabs-card>
        </div>

      </div>
    </div>

    <notifications group="tabSection" position="bottom right"/>
  </div>
</template>

<script>
import { NavTabsCard } from "@/components";

export default {
  components: {
    NavTabsCard
  },

  data() {
      return{
          logged:false,
          car_id:0,
          userComment:[],

          carName:{},
          carDescription:{},
          cars:{},
          user:{}, //l'utilisateur connecté
          
          dataForm:{},

          tab_activated:'tab-description',
      }
  },

  created() {
    this.getCars()
    this.checkUser()
  },

  methods: {
    checkUser(){
      if(this.$session.exists('user')){
        this.logged = true
        this.user = this.$session.get('user')
        this.tab_activated = 'tab-comment'
      }else{
        this.logged = false
        this.tab_activated = 'tab-description'
      }
    },

    getCars() {
      axios.get(this.$apiUrl + 'cars')
        .then(response => {
            this.loadCars(response.data)
        });
    },

    loadCars(cars){
      this.car_id = 0
      this.cars = cars
      this.carName = {}
      this.carDescription = {}
      this.userComment = {}

      this.cars.forEach(car => {
        this.carName[car.id] = car.name
        this.carDescription[car.id] = car.description
      });
    },

    saveComment(){
      var dataForm = {}
      dataForm['user_id'] = this.user.id
      dataForm['car_id'] = this.car_id
      dataForm['comment'] = this.userComment[this.car_id]

      axios.post(this.$apiUrl + 'comment', dataForm)
        .then(response => {
            this.checkUser()            
            this.loadCars(response.data)
        });
    },

    saveCar(){
      var dataForm = {}
      dataForm['id'] = this.car_id
      dataForm['user_id'] = this.user.id
      dataForm['name'] = this.carName[this.car_id]
      dataForm['description'] = this.carDescription[this.car_id]

      axios.put(this.$apiUrl + 'car/' + this.car_id, dataForm)
        .then(response => {
            this.checkUser()
            this.loadCars(response.data)
            this.notify('Enregistrement réussi.', 'success')
        });
    },

    notify(text, type) {
      this.$notify({
          group: 'tabSection',
          duration:5000,
          text: text,
          type: type,
      });
    },
  },
};
</script>