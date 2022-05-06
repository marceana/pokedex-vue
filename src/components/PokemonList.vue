<template>
  <div class="list">
    <article
      v-for="(pokemon, index) in pokemons"
      :key="'poke' + index"
      @click="setPokemonUrl(pokemon.url)"
    >
      <h3 class="poke-number">#{{index}}</h3>
      <img
        :src="imageUrl + pokemon.id + '.png'"
        width="96"
        height="96"
        alt=""
      />
      <h3>{{ pokemon.name }}</h3>
    </article>
    <div id="scroll-trigger" ref="infinitescrolltrigger">
      <i class="fas fa-spinner fa-spin"></i>
    </div>
  </div>
</template>

<script>
export default {
  props: ["imageUrl", "apiUrl"],
  data: () => {
    return {
      pokemons: [],
      nextUrl: "",
      currentUrl: "",
    };
  },
  methods: {
    fetchData() {
      let req = new Request(this.currentUrl);
      fetch(req)
        .then((resp) => {
          if (resp.status === 200) return resp.json();
        })
        .then((data) => {
          this.nextUrl = data.next;
          data.results.forEach((pokemon) => {
            pokemon.id = pokemon.url
              .split("/")
              .filter(function (part) {
                return !!part;
              })
              .pop();
            this.pokemons.push(pokemon);
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    scrollTrigger() {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.intersectionRatio > 0 && this.nextUrl) {
            this.next();
          }
        });
      });
      observer.observe(this.$refs.infinitescrolltrigger);
    },
    next() {
      this.currentUrl = this.nextUrl;
      this.fetchData();
    },
    setPokemonUrl(url) {
      this.$emit("setPokemonUrl", url);
    },
  },
  created() {
    this.currentUrl = this.apiUrl;
    this.fetchData();
  },
  mounted() {
    this.scrollTrigger();
  },
};
</script>

<style lang="scss" scoped>
.list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  grid-gap: 10px;
  width: 100%;
  max-width: 1250px;
  article {
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 250px;
    background-color: #efefef;
    text-align: center;
    text-transform: capitalize;
    border-radius: 5px;
    cursor: pointer;
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2), 0 10px 10px rgba(0, 0, 0, 0.2);
    h3 {
      margin: 0;
    }
    .poke-number{
      position: absolute;
      background-color: rgb(220, 139, 67);
      padding: 10px;
      border-radius: 50%;
      width: 10%;
      align-self: start;
      margin-left: 10px;
      top: 15px;
      
    }
  }
}
#scroll-trigger {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 150px;
  font-size: 2rem;
  color: #efefef;
}
</style>
