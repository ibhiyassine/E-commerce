<script>
import ProductItem from './components/ProductItem.vue'
export default {
  name: 'App',
  components: {
    ProductItem,
  },
  computed: {
    categories() {
      let cats = []
      for (let element of this.database) {
        if (!cats.includes(element.categorie)) {
          cats.push(element.categorie)
        }
      }
      return cats
    },
    produits() {
      let copy = [...this.database] // Creates a copy of the database
      if (this.filter == 0) {
        return copy
      } else if (this.filter == 1) {
        copy.sort((a, b) => a.prix - b.prix)
      } else {
        copy.sort((a, b) => b.prix - a.prix)
      }
      return copy
    },
  },
  data() {
    return {
      database: [],
      dispo: false,
      search: '',
      cat: '',
      filter: 0,
      cart: [],
    }
  },
  methods: {
    addToCart(id) {
      if(this.database[id - 1].Disponible)
      this.cart.push(this.database[id - 1]);
    }
  },
  mounted() {
    fetch('/pieces-autos.json')
      .then((response) => response.json())
      .then((data) => {
        this.database = data
      })
  },
}
</script>

<template>
  <div>
    <header>
      <h1>Les Bonnes Pièces</h1>
    </header>
    <main>
      <!-- Menu de recherche -->
      <section class="filtres">
        <h3>Filtres</h3>
        <div class="form-floating mb-3">
          <input
            type="text"
            class="form-control"
            id="floatingInput"
            v-model="search"
            placeholder="Rechercher"
          />
          <label for="floatingInput">Rechercher</label>
        </div>
        <div class="form-check form-switch mb-3">
          <input
            class="form-check-input"
            type="checkbox"
            id="flexSwitchCheckDefault"
            v-model="dispo"
          />
          <label class="form-check-label" for="flexSwitchCheckDefault">Disponibilité</label>
        </div>
        <div id="category" class="mb-3">
          <select v-model="cat" class="form-select">
            <option value="">Sélectionner une catégorie</option>
            <option v-for="(element, index) in categories" :key="index" :value="element">
              {{ element }}
            </option>
          </select>
        </div>
        <div id="trier">
          <select v-model="filter" class="form-select">
            <option value="0">Pas de filtres</option>
            <option value="1">Trier par ordre de prix croissant</option>
            <option value="2">Trier par ordre de prix décroissant</option>
          </select>
        </div>
        <hr />
        <h3>Panier</h3>
        <ul class="list-group">
          <li
            v-for="(element, index) of cart"
            class="list-group-item d-flex justify-content-between"
          >
            <div>
              {{ element.nom }}
            </div>
            <div>{{ element.prix }}$</div>
          </li>
        </ul>
      </section>

      <!-- Fiches produits -->
      <section class="fiches d-flex flex-wrap gap-3">
        <div v-for="(element, index) in produits" :key="index">
          <ProductItem
            :produit="element"
            v-if="
              element.nom.toLowerCase().includes(search.toLowerCase()) &&
              !(dispo && !element.Disponible) &&
              !(cat != '' && element.categorie != cat)
            "
            :addToCart="addToCart"
          />
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat&display=swap');

body {
  max-width: 1200px;
  margin: auto;
  padding: 16px;
  font-family: 'Montserrat', sans-serif;
}

header {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 16px;
  padding: 8px;
  background-color: #7451eb;
  text-align: center;
  color: white;
}
header img {
  height: 60px;
  margin-left: 1rem;
}
header h1 {
  flex-grow: 1;
}

main {
  display: flex;
  flex-direction: row;
}

.filtres {
  border-radius: 4px;
  box-shadow: 0px 0px 4px gray;
  margin: 8px;
  padding: 8px;
  min-width: 300px;
  min-height: 400px;
}
.filtres h3 {
  text-align: center;
}

.fiches {
  flex: 1;
  margin: 8px;
}
.fiches img {
  max-width: 200px;
}
.fiches p {
  margin-top: 4px;
  margin-bottom: 4px;
}
</style>
