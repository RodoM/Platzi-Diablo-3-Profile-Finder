<template>
  <div class="profile-view">
    <BaseLoading v-if="isLoading"/>
    <template v-if="profileData !== null">
      <MainBlock :profile-data="profileData"/>
      <ArtisansBlock :artisans-data="artisansData" />
    </template>
  </div>
</template>

<script>
import setError from '@/mixins/setError'
import { getApiAccount } from '@/api/search'

import BaseLoading from '@/components/BaseLoading.vue'
import MainBlock from './MainBlock/Index-view.vue'

import ArtisansBlock from './ArtisansBlock/Index-view.vue'

export default {
  name: 'ProfileView',
  components: { BaseLoading, MainBlock, ArtisansBlock },
  mixins: [
    setError
  ],
  data () {
    return {
      isLoading: false,
      profileData: null
    }
  },
  created () {
    this.isLoading = true
    const { region, battleTag: account } = this.$route.params
    this.fetchData(region, account)
  },
  methods: {
    fetchData () {
      // Desestructuración
      const { region, battleTag: account } = this.$route.params
      // Llamada a la API con los datos necesarios
      getApiAccount({ region, account })
        .then(({ data }) => {
          this.profileData = data
        })
        .catch((err) => {
          this.profileData = null
          // Creamos el objeto error
          const errObj = {
            routeParams: this.$route.params,
            message: err.message
          }
          if (err.response) {
            errObj.data = err.response.data
            errObj.status = err.response.status
          }
          // Hacemos uso del Mixin
          // Guardamos el objeto en el Store
          this.setApiErr(errObj)
          // Navegamos a la ruta de nombre 'Error'
          this.$router.push({ name: 'Error' })
        })
        .finally(() => {
          this.isLoading = false
        })
    }
  },
  computed: {
    artisansData () {
      return {
        blacksmith: this.profileData.blacksmith,
        blacksmithHardcore: this.profileData.blacksmithHardcore,
        jeweler: this.profileData.jeweler,
        jewelerHardcore: this.profileData.jewelerHardcore,
        mystic: this.profileData.mystic,
        mysticHardcore: this.profileData.mysticHardcore
      }
    }
  }
}
</script>
