<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated color="positive">
      <q-toolbar>
        <q-toolbar-title class="q-mx-auto" style="font-family: 'Poppins'; font-style: italic;">
          Berita Terkini
        </q-toolbar-title>

        <!-- Kotak Pencarian -->
        <div class="q-ml-md">
          <q-input
            v-model="searchTerm"
            label="Cari berita"
            color="white"
            dense
            @input="searchNews"
          >
            <template v-slot:append>
              <q-btn
                flat
                round
                dense
                color="white"
                icon="search"
                aria-label="Cari"
                @click="searchNews"
              />
            </template>
          </q-input>
        </div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <q-card-section>
      <div class="q-pa-md row items-start q-gutter-md" style="justify-content: center;">
      <q-col
        v-for="(article, index) in filteredBeritaList" :key="index" class="q-mb-md" style="width: 450px"
        :span-xs="12"
        :span-sm="6"
        :span-md="4"
      >
        <q-card class="my-card" clickabel @click="tampilkanDetailBerita(article)" style="cursor: pointer;">
          <q-img :src="article.urlToImage" alt=" " >
            <div class="absolute-bottom text-h6">
              {{ article.title }}
            </div>
          </q-img>

          <q-card-section style="font-family: 'Poppins'; font-size: 1.4rem;">
            {{ article.description }}
          </q-card-section>
        </q-card>
      </q-col>
    </div>
    </q-card-section>
  </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted, computed } from 'vue'
import axios from 'axios'

export default defineComponent({
  name: 'MainLayout',

  setup () {
    const beritaList = ref([])
    const searchTerm = ref('')
    const categories = ref(['business', 'health', 'sports', 'entertainment', 'science'])

    const tampilkanDetailBerita = (berita) => {
      window.open(berita.url)
    }

    const toggleSearchBar = () => {}

    const fetchNews = async () => {
      try {
        const apiKey = '959823a4ed2f4f9095156ec4c82fd957'
        const apiUrl = 'https://newsapi.org/v2/top-headlines'

        const responses = await Promise.all(
          categories.value.map(async (category) => {
            const response = await axios.get(apiUrl, {
              params: {
                country: 'us',
                category: [category]
              },
              headers: {
                'X-Api-Key': apiKey
              }
            })
            return response.data.articles
          })
        )

        const combinedArticles = responses.flat()

        beritaList.value = combinedArticles
      } catch (error) {
        console.error('Gagal mengambil berita:', error)
      }
    }

    // Ambil daftar berita saat komponen dibuat
    onMounted(() => {
      fetchNews()
    })

    const filteredBeritaList = computed(() => {
      return beritaList.value.filter((article) =>
        article.title.toLowerCase().includes(searchTerm.value.toLowerCase())
      )
    })

    const searchNews = () => {
      // Implementasi pencarian berita di sini
    }

    return {
      beritaList,
      tampilkanDetailBerita,
      searchTerm,
      toggleSearchBar,
      filteredBeritaList,
      searchNews,
      categories
    }
  }
})
</script>
