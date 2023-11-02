<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated color="positive">
      <q-toolbar>
        <q-toolbar-title class="q-mx-auto" style="font-family: 'Poppins'; font-style: italic;">
          Berita Terkini
        </q-toolbar-title>

        <div class="q-ml-md" style="width: 15rem;">
         <q-select
         outlined
         v-model="model"
         bg-color="white"
         :options="categories"
         :dense="true"
         label="Category Berita"
         @change="onCategoryChange"
         />
       </div>
        <!-- Kotak Pencarian -->
        <div class="q-ml-md" style="margin-right: 1.5rem;">
          <q-input
            v-model="searchTerm"
            label="Cari berita"
            color="white"
            dense
            @input="searchNews"
            @keyup.enter="searchNews"
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
          <div
            v-for="(article, index) in beritaList" :key="index" class="q-mb-md" style="width: 450px"
          >
            <q-card class="my-card" clickable @click="tampilkanDetailBerita(article)" style="cursor: pointer;">
              <q-img :src="article.urlToImage" alt=" ">
                <div class="absolute-bottom text-h6">
                  {{ article.title }}
                </div>
              </q-img>

              <q-card-section style="font-family: 'Poppins'; font-size: 1.4rem;">
                {{ article.description }}
              </q-card-section>
            </q-card>
          </div>
        </div>
      </q-card-section>
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
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

    const fetchNews = async () => {
      try {
        const apiKey = '959823a4ed2f4f9095156ec4c82fd957'
        const apiUrl = 'https://newsapi.org/v2/top-headlines'

        const responses = await Promise.all(
          categories.value.map(async (category) => {
            const response = await axios.get(apiUrl, {
              params: {
                country: 'us',
                category: { category }
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

    const searchNews = async () => {
      try {
        const apiKey = '959823a4ed2f4f9095156ec4c82fd957'
        const apiUrl = 'https://newsapi.org/v2/everything'

        const response = await axios.get(apiUrl, {
          params: {
            q: searchTerm.value,
            apiKey
          }
        })

        if (response.data.status === 'ok') {
          const searchResult = response.data.articles
          const combinedArticles = searchResult
          beritaList.value = combinedArticles
        } else {
          console.error('Terjadi kesalahan dalam pencarian berita.')
        }
      } catch (error) {
        console.error('Gagal mencari berita:', error)
      }
    }

    const onCategoryChange = () => {
      fetchNews()
    }
    onMounted(() => {
      fetchNews()
    })

    return {
      beritaList,
      tampilkanDetailBerita,
      searchTerm,
      categories: ['business', 'health', 'sports', 'entertainment', 'science'],
      searchNews,
      onCategoryChange
    }
  }
})
</script>
