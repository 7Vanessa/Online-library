<template>
  <form @submit.prevent="addArticle" class="card">
    <h1 class="card__title">Nouveau livre à ajouter</h1>
    <div class="form-row">
      <input class="form-row__input" type="text" v-model="newArticle.titre" placeholder="Titre du livre" required>
    </div>
    <div class="form-row">
      <input class="form-row__input" type="text" v-model="newArticle.image" placeholder="Lien de l'image du livre" required>
    </div>
    <div class="form-row">
      <input class="form-row__input" type="text" v-model="newArticle.auteur" placeholder="Auteur du livre" required>
    </div>
    <div class="form-row_b">
      <label>Nombre de pages du livre:</label>
      <input class="form-row__input" type="number" v-model="newArticle.nb_pages" min="0" placeholder="Nombre de pages du livre" required>
    </div>
    <div class="form-row">
      <textarea class="form-row__input" type="text" v-model="newArticle.description" placeholder="Description du livre" required></textarea>
    </div>
    <div class="form-row_b">
      <label>Nombre d'exemplaires du livre:</label>
      <input class="form-row__input" type="number" v-model="newArticle.nb_exemplaires" placeholder="Nombre d'exemplaires du livre" >
    </div>
    <div class="form-row">
      <button type="reset" v-on:click="annuler">Annuler</button>
      <button type="submit" v-on:click="addArticle(newArticle)">Ajouter</button>
    </div>
  </form>
</template>

<script>
module.exports = {
  data(){
    return {
      newArticle: {
        titre: '',
        image: '',
        auteur: '',
        nb_pages: 0,
        description: '',
        nb_exemplaires: 0
      }
    }
  },

  methods: {
    async addArticle(article) {
      await axios.post('/api/article', article).then(async response => {
        alert("Le livre a été ajouté !")
        window.location.reload()
      }).catch(error => {
        this.invaliddata = true
      });
    },
    annuler() {
      window.location.reload()
    }
  }

}
</script>

<style scoped>

.card {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
  border: 1px solid black;
  padding: 10px;
  border-radius: 20px;
  margin-top: 130px;
  background-color: white;
}
.form-row {
  display: flex;
  margin: 16px 0px;
  gap: 16px;
  flex-wrap: wrap;
  justify-content: center;
}

.form-row_b {
  display: flex;
  flex-direction: column;
  text-align: left;
  margin: 16px 0px;
  gap: 16px;
  flex-wrap: wrap;
  justify-content: center;
}

.form-row__input {
  padding:8px;
  border: none;
  border-radius: 8px;
  background:#f2f2f2;
  font-weight: 500;
  font-size: 16px;
  flex:1;
  min-width: 100px;
  color: black;
  max-width: 500px;
}
.form-row__input::placeholder {
  color:#aaaaaa;
}
button {
  height: 30px;
  border-radius: 20px;
  background-color: #04729d;
  border: none;
  color: white;
  font-size: 17px;
}

</style>