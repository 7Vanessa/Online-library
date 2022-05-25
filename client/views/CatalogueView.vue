<template>
  <div class="container">
    <div class="livres">
      <div class="recherche">
        <input class="form-row__input" type="text" placeholder="Rechercher un livre" v-model="recherche">
        <img v-on:click="reload" src="../assets/croix.svg">
        <button v-on:click="rechercherLivre">Rechercher</button>
      </div>

      <article v-if="!isSearching" v-for="article in articles" :key="article.id">
        <div class="container_child">
          <div class="card">
            <div class="article-img">
              <img v-bind:src="article.image">
            </div>
            <div class="card_right">
              <h3><strong>{{article.titre}}</strong></h3>
              <p>Auteur: <strong>{{article.auteur}}</strong></p>
              <p>Nb pages: <strong>{{article.nb_pages}}</strong></p>
              <div>
                <p v-if="article.nb_exemplaires>0"><strong>{{article.nb_exemplaires}} exemplaires restants</strong></p>
                <p v-else><strong>Indisponible</strong></p>
              </div>
              <p>{{article.description}}</p>
            </div>
            <div class="right_container">
              <div class="quantity_select">
                <p>Quantité:</p>
                <input type="number" min="1" v-model.number="qt">
              </div>
              <div v-if="article.nb_exemplaires>0" class="add_button">
                <button v-on:click="addPanier({id_livre: article.id_livre, quantity: qt, nb_exemplaires: article.nb_exemplaires})">Ajouter au panier</button>
              </div>
            </div>
          </div>
          <div class="button_admin">
            <button v-on:click="suppressionLivre(article)" v-if="user.statut=='A'">Supprimer le livre du catalogue</button>
          </div>
        </div>
      </article>


      <article v-if="isSearching" v-for="article in liste" :key="article.id">
        <div class="container_child">
          <div class="card">
            <div class="article-img">
              <img v-bind:src="article.image">
            </div>
            <div class="card_right">
              <h3><strong>{{article.titre}}</strong></h3>
              <p>Auteur: <strong>{{article.auteur}}</strong></p>
              <p>Nb pages: <strong>{{article.nb_pages}}</strong></p>
              <div>
                <p v-if="article.nb_exemplaires>0"><strong>{{article.nb_exemplaires}} exemplaires restants</strong></p>
                <p v-else><strong>Indisponible</strong></p>
              </div>
              <p>{{article.description}}</p>
            </div>
            <div class="right_container">
              <div class="quantity_select">
                <p>Quantité:</p>
                <input type="number" min="1" v-model.number="qt">
              </div>
              <div v-if="article.nb_exemplaires>0" class="add_button">
                <button v-on:click="addPanier({id_livre: article.id_livre, quantity: qt, nb_exemplaires: article.nb_exemplaires})">Ajouter au panier</button>
              </div>
            </div>
          </div>
          <div class="button_admin">
            <button v-on:click="suppressionLivre(article)" v-if="user.statut=='A'">Supprimer le livre du catalogue</button>
          </div>
        </div>
      </article>

    </div>

    <div v-if="user.statut=='A'" class="formulaire">
      <add-article v-if="formulaire"> </add-article>
      <button v-on:click="switchForm" v-else>Ajouter un livre à la bibliothèque</button>
    </div>
  </div>
</template>

<script>
const AddArticle = window.httpVueLoader('../components/AddArticle.vue')

module.exports = {
  components: {
    AddArticle,
  },
  props: {
    articles: { type: Array, default: [] },
    panier: { type: Object },
    user: { type: Object }
  },
  data () {
    return {
      formulaire: false,
      recherche: '',
      liste: [],
      isSearching: false
    }
  },
  async mounted() {

  },
  methods: {
    switchForm() {
      this.formulaire = true
    },

    filtreTexte(arr, requete) {
      return arr.filter(el =>  el.titre.toLowerCase().indexOf(requete.toLowerCase()) !== -1);
    },

    deleteArticle (articleId) {
      this.$emit('delete-article', articleId)
    },

    async suppressionLivre(data) {
      await axios.post('/api/articles/' + data.id_livre, data).then(async response => {
        alert("Le livre à été supprimé de la base de donnée")
      }).catch(error => {
        alert('Erreur: le livre n\'a pas été supprimé !')
      }).finally(window.location.reload())
    },

    async addPanier(data) {
      if(data.quantity>data.nb_exemplaires) {
        alert("La quantité demandée est supérieure au nombre d'exemplaires disponibles !")
        return
      }
      if(data.quantity<=0) {
        alert("La quantité demandé est insuffisante !")
      }
      await axios.post('/api/panier', data).then(async response => {
        alert("Le livre à été ajouté au panier")
      }).catch(error => {
        alert('Erreur: le livre n\'a pas été ajouté au panier !')
      });
    },

    rechercherLivre() {
      this.liste = this.articles
      this.liste = this.filtreTexte(this.liste, this.recherche)
      this.isSearching = true
    },

    reload() {
      window.location.reload()
    }
  }
}
</script>

<style scoped>

.container {
  width: 100%;
  margin-top: 20px;
}

.livres {
  margin-bottom: 50px;
}

article {
  display: flex;
}

.article-img {
  flex: 1;
}

.article-img div {
  width: 100px;
  height: 100px;
  background-size: cover;
}

.formulaire {
  margin-left: auto;
  margin-right: auto;
  margin-right: auto;
}

.formulaire button {
  height: 30px;
  background-color: #239aff;
  border-radius: 5px;
  font-size: 15px;
  border: none;
}

.recherche button {
  height: 30px;
  background-color: #239aff;
  border-radius: 5px;
  font-size: 15px;
  border: none;
}

.recherche img {
  width: 50px;
  height: auto;
}

.recherche {
  display: flex;
  align-items: center;
  width: 80%;
  margin-left: auto;
  margin-right: auto;
}

.form-row__input {
  padding:8px;
  border: none;
  border-radius: 8px;
  background:#f2f2f2;
  font-weight: 500;
  font-size: 16px;
  flex:1;
  width: 1000px;
  color: black;
}

.form-row__input::placeholder {
  color:#aaaaaa;
}

.container_child {
  display: flex;
  justify-content: space-around;
  width: 100%;
  border-bottom: 1px solid black;
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
  width: 600px;
  justify-content: left;
  text-align: left;
}

.right_container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding-left: 40px;
  margin-left: 50px;
}

.quantity_select {
  margin-bottom: 20px;
}

.quantity_select input {
  width: 50px;
  height: 30px;
  margin-top: -10px;
}

.add_button button {
  height: 30px;
  background-color: #ffcb01;
  border-radius: 5px;
  font-size: 15px;
  border: none;
}

.button_admin {
  display: flex;
  align-items: center;
}

.button_admin button {
  height: 30px;
  background-color: #ff4114;
  border-radius: 5px;
  font-size: 15px;
  font-weight: bold;
  border: none;
}

</style>
