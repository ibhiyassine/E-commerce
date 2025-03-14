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
    filteredProducts() {
      return this.produits.filter(element => 
        element.nom.toLowerCase().includes(this.search.toLowerCase()) &&
        !(this.dispo && !element.Disponible) &&
        !(this.cat !== '' && element.categorie !== this.cat)
      )
    },
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage)
    },
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage
      const endIndex = startIndex + this.itemsPerPage
      return this.filteredProducts.slice(startIndex, endIndex)
    },
    displayedPages() {
      const pages = []
      const startPage = Math.max(1, this.currentPage - 2)
      const endPage = Math.min(this.totalPages, startPage + 4)
      
      for (let i = startPage; i <= endPage; i++) {
        pages.push(i)
      }
      
      return pages
    }
  },
  data() {
    return {
      database: [],
      dispo: false,
      search: '',
      cat: '',
      filter: 0,
      cart: [],
      currentPage: 1,
      itemsPerPage: 6
    }
  },
  methods: {
    addToCart(id) {
      if (this.database[id - 1].Disponible) {
        this.cart.push(this.database[id - 1])
      }
    }
  },
  watch: {
    search() {
      this.currentPage = 1
    },
    dispo() {
      this.currentPage = 1
    },
    cat() {
      this.currentPage = 1
    },
    filter() {
      this.currentPage = 1
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
        <div id="trier" class="mb-3">
          <select v-model="filter" class="form-select">
            <option value="0">Pas de filtres</option>
            <option value="1">Trier par ordre de prix croissant</option>
            <option value="2">Trier par ordre de prix décroissant</option>
          </select>
        </div>
        
        <h3>Panier</h3>
        <div id="panier">
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
            <li v-if="cart.length === 0" class="list-group-item text-center text-muted">
              Panier vide
            </li>
          </ul>
        </div>
      </section>

      <div class="product-container">
        <section class="fiches">
          <div v-for="(element, index) in paginatedProducts" :key="index">
            <ProductItem
              :produit="element"
              :addToCart="addToCart"
            />
          </div>
        </section>
        
        <div class="pagination">
          <button 
            @click="currentPage = Math.max(currentPage - 1, 1)"
            :disabled="currentPage === 1 || totalPages <= 1"
          >
            &laquo;
          </button>
          <button 
            v-for="page in displayedPages" 
            :key="page"
            @click="currentPage = page"
            :class="{ active: currentPage === page }"
          >
            {{ page }}
          </button>
          <button 
            @click="currentPage = Math.min(currentPage + 1, totalPages)"
            :disabled="currentPage === totalPages || totalPages <= 1"
          >
            &raquo;
          </button>
        </div>
      </div>
    </main>
  </div>
</template>