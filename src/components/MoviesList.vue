<template>
  <BContainer>
    <h3 class="list-title">{{listTitle}}</h3>
    <BRow>
      <template v-if="isExist">
        <BCol cols="3" v-for="(movie, key) in list" :key="key">
          <MovieItem :movie="movie" @mouseover.native="onMouseOver(movie.Poster)" @removeItem="onRemoveItem"/>
        </BCol>
      </template>
      <template v-else>
        <div>Empty list</div>
      </template>
    </BRow>
  </BContainer>
</template>

<script>
import {mapActions, mapGetters} from 'vuex';
import MovieItem from '@/components/MovieItem';
export default {
  name: 'MoviesList',
  props: {
    list: {
      type: Object,
      default: () => ({})
    }
  },
  computed: {
    ...mapGetters('movies', ['isSearch']),
    isExist() {
      return Boolean(Object.keys(this.list).length);
    },
    listTitle() {
      return this.isSearch ? "Search result" : "IMDB top 250"
    }
  },
  components: {
    MovieItem
  },
  methods: {
    ...mapActions('movies', ['removeMovie']),
    ...mapActions(['showNotify']),
    onMouseOver(poster) {
      this.$emit('changePoster', poster)
    },
    async onRemoveItem({id, title}) {
      const isConfirmed = await this.$bvModal.msgBoxConfirm(`Are you sure delete ${title}?`);
      if(isConfirmed) {
        this.removeMovie(id);
        this.showNotify({
          msg: "Movie deleted successful",
          variant: "success",
          title: "Success"
        })
      }
    }
  }
}
</script>

<style scoped>
  .list-title {
    font-size: 50px;
    margin-bottom: 50px;
    color: #fff;
  }
</style>