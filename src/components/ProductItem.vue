<script>
export default {
  props: {
    produit: {
      default: {
        nom: 'gateau',
        categorie: 'delices',
        prix: '150',
        id: '-1',
      },
    },
    addToCart: {
        type: Function,
        required: true,
        default: ()=>{
            console.log("Empty");
        }
    }
  },
  methods: {
    add(id){
        console.log("Hello")
        this.addToCart(id);
    }
  },
}
</script>

<template>
  <div class="card">
    <img :src="produit.Image" class="card-img-top" alt="Image du produit" />
    <div class="card-body">
      <h5 class="card-title">{{ produit.nom }}</h5>
      <h6 class="card-subtitle mb-2 text-muted">
        <span :class="produit.Disponible ? 'text-success' : 'text-danger'">
          {{ produit.Disponible ? "Disponible" : "En rupture" }}
        </span>, 
        <span class="fw-bold">{{ produit.prix }}$</span>
      </h6>
      <button 
        class="btn" 
        :class="produit.Disponible ? 'btn-outline-success' : 'btn-danger'" 
        @click="add(produit.id)"
        :disabled="!produit.Disponible"
      >
        {{ produit.Disponible ? 'Add to cart' : 'En rupture' }}
      </button>
    </div>
  </div>
</template>

