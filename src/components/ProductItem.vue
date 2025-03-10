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
  <div class="card" style="width: 16rem">
    <img :src="produit.Image" class="card-img-top img-thumbnail" alt="..." />
      <div class="card-body">
    <h5 class="card-title">{{ produit.nom }}</h5>
    <h6 class="card-subtitle mb-2 text-muted">{{ produit.Disponible ? "Disponible" : "En rupture" }}, {{ produit.prix}}$</h6>
    <button href="" class="btn" :class="produit.Disponible ? 'btn-outline-success' : 'btn-danger'" 
    @click="add(produit.id)"
    >Add to cart</button>
  </div>
  </div>
</template>

<style scoped>
.btn-danger:hover{
    cursor: not-allowed;
    background-color: #dc3545;
}
</style>
