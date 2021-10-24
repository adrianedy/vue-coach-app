<template>
  <div v-if="isLoading" class="loading-page">
    <base-spinner></base-spinner>
  </div>
  <not-found v-if="!isLoading && selectedCoach == null"></not-found>
  <div v-else-if="!isLoading">
    <section>
      <base-card>
        <h2>{{ fullName }}</h2>
        <h3>${{ rate }}/hour</h3>
      </base-card>
    </section>
    <section>
      <base-card>
        <header>
          <h2>Interested? Reach out now!</h2>
          <base-button link :to="contactLink">Contact</base-button>
        </header>
        <router-view></router-view>
      </base-card>
    </section>
    <section>
      <base-card>
        <base-badge v-for="area in areas" :key="area" :type="area" :title="area"></base-badge>
        <p>{{ description }}</p>
      </base-card>
    </section>
  </div>
</template>

<script>
import NotFound from '../NotFound.vue';

export default {
  components: {
    NotFound
  },
  props: ['id'],
  data() {
    return {
      selectedCoach: null,
      isLoading: true,
    };
  },
  computed: {
    fullName() {
      return this.selectedCoach.firstName + ' ' + this.selectedCoach.lastName;
    },
    areas() {
      return this.selectedCoach.areas;
    },
    rate() {
      return this.selectedCoach.hourlyRate;
    },
    description() {
      return this.selectedCoach.description;
    },
    contactLink() {
      return this.$route.path + '/' + this.id + '/contact';
    },
  },
  async beforeMount() {
    this.isLoading = true;
    this.selectedCoach = null;
    await this.loadCoaches(true);

    this.selectedCoach = this.$store.getters['coaches/coaches'].find(
      (item) => item.id === this.id
    );

    this.isLoading = false;
  },
  methods: {
    async loadCoaches(refresh = false) {
      try {
        await this.$store.dispatch('coaches/loadCoaches', {
          forceRefresh: refresh,
        });
      } catch (error) {
        this.error = error.message || 'Something went wrong!';
      }
    },
  },
};
</script>


<style scoped>
.loading-page {
  display:flex;
  align-items:center;
  justify-content:center;
  height:calc(100vh - 5rem);
}
</style>