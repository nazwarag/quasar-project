<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-positive">
      <q-toolbar>
        <q-toolbar-title class="font-title">
          Berita Terkini
        </q-toolbar-title>

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

      <div class="q-pa-md q-gutter-lg">
        <q-banner inline-actions rounded class="bg-white">
            <a
              v-for="category in categoryOptions"
              :key="category.value"
              dense
              @click="selectCategory(category.value)"
              :class="[
                'q-mx-sm',
                'cursor-pointer',
                selectedCategory === category.value ? 'font-weight-bold text-black' : 'font-weight-regular text-positive'
              ]"
            >
              {{ category.label }}
            </a>
          </q-banner>
        </div>
    </q-header>

    <q-page-container>
      <q-card-section>
        <div class="q-pa-md row items-start q-gutter-md" style="justify-content: center;">
          <div
            v-for="(article, index) in beritaList"
            :key="index"
            class="q-mb-md"
            style="width: 450px"
          >
            <q-card
              class="my-card"
              clickable
              @click="tampilkanDetailBerita(article)"
              style="cursor: pointer;"
            >
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
    const selectedCategory = ref('general')
    const categoryOptions = ref([
      { label: 'General', value: 'general' },
      { label: 'Business', value: 'business' },
      { label: 'Health', value: 'health' },
      { label: 'Sports', value: 'sports' },
      { label: 'Entertainment', value: 'entertainment' },
      { label: 'Science', value: 'science' }
    ])

    const tampilkanDetailBerita = (berita) => {
      window.open(berita.url)
    }

    const fetchNews = async () => {
      try {
        const apiKey = '959823a4ed2f4f9095156ec4c82fd957'
        const apiUrl = 'https://newsapi.org/v2/top-headlines'

        const response = await axios.get(apiUrl, {
          params: {
            country: 'us',
            category: selectedCategory.value
          },
          headers: {
            'X-Api-Key': apiKey
          }
        })

        if (response.data.status === 'ok') {
          beritaList.value = response.data.articles
        } else {
          console.error('Terjadi kesalahan dalam mengambil berita.')
        }
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
          beritaList.value = response.data.articles
        } else {
          console.error('Terjadi kesalahan dalam pencarian berita.')
        }
      } catch (error) {
        console.error('Gagal mencari berita:', error)
      }
    }

    const selectCategory = async (category) => {
      selectedCategory.value = category
      await fetchNews()
    }

    onMounted(() => {
      fetchNews()
    })

    return {
      beritaList,
      tampilkanDetailBerita,
      searchTerm,
      selectedCategory,
      categoryOptions,
      searchNews,
      selectCategory
    }
  }
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@500&display=swap');
  .font-title {
    font-family: 'Poppins', sans-serif;
    font-weight: 500;
    font-size: 3rem;
    margin-left: 20px;
  }
</style>
