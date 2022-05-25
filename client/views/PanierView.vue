<template>
  <div class="container">
    <div class="router">
      <nav>
        <router-link to="/catalogue">Retour au catalogue</router-link>
      </nav>
    </div>
    <div class="title">
      <h2>Mon Panier</h2>
    </div>

    <article class="panier" v-for="livre in panier">

      <div class="container_child">
        <div class="card">
          <div class="article-img">
            <img v-bind:src="livre.image">
          </div>
          <div class="card_right">
            <h3><strong>{{livre.titre}}</strong></h3>
            <p>Auteur: <strong>{{livre.auteur}}</strong></p>
          </div>
          <div class="right_container">
            <p>Quantité: {{livre.quantity}}</p>
            <img v-on:click="removePanier(livre)" class="trash" src="../assets/poubelle.svg">
          </div>
        </div>
      </div>
    </article>

    <div class="add_button">
      <button v-on:click="pay({livres: panier})">Valider le panier</button>
    </div>
  </div>
</template>

<script>

const PanierItem = window.httpVueLoader('../components/PanierItem.vue')

module.exports = {
  props: {
  },
  components: {
    PanierItem
  },
  data () {
    return {
      panier: [],
    }
  },
  async mounted () {
    await axios.get('/api/panier').then(async response => {
      this.panier = response.data
    }).catch(error => {
      alert(error)
    });

  },
  methods: {
    async pay(data) {
      await axios.post('/api/panier/pay', data).then(async response => {
      }).catch(error => {
        alert('Bonne lecture !')
        window.location.reload()
      });
    },

    async removePanier(data) {
      await axios.post('/api/panier/' + data.id_livre, data).then(async response => {
        window.location.reload()
        alert('le livre a été supprimé du panier !')
      }).catch(error => {
        alert('La suppression a échoué !')
      }).finally(window.location.reload());
    }
  }
}
</script>

<style scoped>

.container {
  margin-top: 10px;
  padding-left: 15px;
}

.router {
  display: flex;
  justify-content: left;
}

.panier img {
  width: 15px;
  height: auto;
}

.add_button button {
  height: 40px;
  background-color: #ffcb01;
  border-radius: 5px;
  font-size: 18px;
  border: none;
  margin-top: 30px;
  font-weight: bold;
}

.title {
  margin-top: 10px;
  margin-bottom: 15px;
}

.container_child {
  display: flex;
  justify-content: left;
  border-bottom: 1px solid black;
  margin-right: 15px;
  margin-left: 15px;
}

.card {
  display: flex;
  flex-direction: row;
  padding: 20px;
  align-items: center;
}

.article-img {
  margin-right: 20px;
  padding-right: 50px;

}

.article-img img {
  background-size: cover;
  width: 110px;
  height: auto;
}

.card_right {
  width: 500px;
  justify-content: left;
  text-align: left;
}

.right_container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  padding-left: 40px;
  margin-left: 50px;
}

.right_container img {
  background-size: cover;
  width: 60px;
  height: auto;
  margin-left: 200px;
}

</style>
