<template>
  <div id="app">
    <div class="flex flex-col min-h-screen">
      <div class="header">

        <!--Search bar-->

        <form @submit="searchRepo">
          <div class="flex flex-col mt-8 space-y-3 sm:space-y-0 sm:flex-row sm:justify-center sm:space-x-4">
            <input id="keyword" v-model="keyword" type="text" class="validatpx-4 py-2 text-gray-700 bg-white border border-gray-300 rounded-md dark:bg-gray-800 dark:text-gray-200 :border-gray-600 focus:border-gray-500 :focus:border-gray-500 focus:outline-none focus:ring placeholder-opacity-80 text-center" placeholder=" Search">

            <button class="px-4 py-2 text-base font-medium tracking-wide text-white capitalize transition-colors duration-200 transform bg-gray-700 rounded-md hover:bg-gray-600 focus:outline-none focus:bg-gray-600 fab fa-github"> SUBMIT</button>
          </div>
        </form>

        <div class="text-center text-gray-500 mt-9 font-mono md:font-seri" v-if="load">Loading...</div>
      </div>

      <!--Search results-->

      <section class="flex-grow text-gray-600 body-font md:font-serif">
        <div class="container px-2 py-10 mx-auto">
          <div v-if="show" class="flex flex-col text-center w-full mb-10">
            <h1 class="text-base font-medium title-font font-mono text-gray-600">Found <span>{{total}}</span>
            {{ total == 1 ? 'repository' : 'repositories'}}</h1>
          </div>

          <div v-if="show" class="flex flex-wrap -m-2">
            <div v-for="repo in repos" class="p-2 lg:w-1/3 md:w-1/2 w-full transition duration-300 ease-in-out">
              <div class="h-full flex items-center border-gray-200 border p-2 rounded-lg shadow-md">
                <a :href="repo.owner.avatar_url" target="_blank" class="w-16 h-16 m-2 flex-shrink-0">
                  <img :src="repo.owner.avatar_url" alt="avatar"  class="object-cover object-center rounded-full">
                </a>

                <div class="flex-grow m-2">
                  <a :href="repo.html_url" target="_blank" class="text-gray-900 font-bold text-lg hover:text-gray-600">{{repo.owner.login}}</a>
                  <p class="text-sm text-gray-500 break-all md:break-all">{{repo.description}}</p>

                  <ul v-if="show" class="inline-flex font-mono text-sm">
                    <li class="m-2" v-if="repo.language"><p :class="repo.language"><i class="fa fa-circle" aria-hidden="true"></i><span>{{repo.language}}</span></p></li>

                    <li class="m-2"><p><i class="fa fa-star" aria-hidden="true"></i><span>{{repo.stargazers_count}}</span></p></li>

                    <li class="m-2" v-if="repo.watchers"><p><i class="fa fa-eye" aria-hidden="true"></i><span>{{repo.watchers}}</span></p>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>

          <!--Pagination-->

          <div v-if="pagination" class="container mx-auto flex flex-wrap my-6 justify-center items-center pt-6" aria-label="Pagination">
            <ul class="flex items-center justify-center">
              <li v-if="page == 1" class="mr-3 p-2 invisible" aria-hidden="true" role="presentation">
                <button @click="prevPage()" disabled class="focus:outline-none"><i class="fa fa-chevron-left"></i></button>
              </li>

              <li v-if="page != 1" class="mr-3 p-2 cursor-pointer hover:text-gray-400" aria-hidden="true" role="presentation">
                <button @click="prevPage()" class="focus:outline-none"><i class="fa fa-chevron-left"></i></button>
              </li>

              <li v-if="page != total" class="p-2 mr-3 cursor-pointer hover:text-gray-400">
                <button @click="nextPage()" class="focus:outline-none"><i class="fa fa-chevron-right"></i></button>
              </li>

              <li v-if="page == total" class="p-2 mr-3 invisible"aria-hidden="true" role="presentation">
                <button @click="nextPage()" disabled class="focus:outline-none"><i class="fa fa-chevron-right"></i></button>
              </li>
            </ul>
          </div>
        </div>
      </section>

      <!-- Footer -->

      <footer class="flex items-center justify-center">
        <p class="font-mono font-sm text-gray-600">
          Made with ❤️ by <a href="https://highflyer910.github.io/" target="_blank" class="font-bold">Teona</a>
        </p>
      </footer>
    </div>
  </div>
</template> 

<script>
import Vue from 'vue';
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios,axios)

export default {
  name: "Search",
  data() {
    return {
      status: '',
      keyword: '',
      total: '',
      repos: [],
      show: false,
      pagination: false,
      error: false,
      load: false,
      page: 1,
      maxPage: 10
    }
  },
  methods: {
    searchRepo: function(event) {
      if(event){
        event.preventDefault()
        this.page = 1
      }
      let vm = this
      let searchWord = this.keyword
      let page = this.page
      let query = 'https://api.github.com/search/repositories?sort=stars&per_page=10&page='+page+'&q='
      vm.load = true

      axios.get(query + searchWord)
      .then(function (response) {
        let total = response.data.total_count
        vm.total = total
        vm.repos = response.data.items

        if( total == 0) {
          vm.error= true
          vm.show = false
          vm.pagination = false
          vm.load = false
          vm.keyword = ''
          alert("Repository not found")
        } else {
          vm.error= false
          vm.show = true
          vm.load = false
          vm.pagination = true
        }

        if(total <= 10){
          vm.pagination = false
        }
      })
      .catch(function (error) {
        vm.status = 'An error occured.' + error;
      })
    },
    nextPage: function() {
      this.page = this.page + 1
      this.searchRepo()
    },
    prevPage: function() {
      this.page = this.page - 1
      this.searchRepo()
    }
  }
}
        


</script>

<style>
i.fa-star {
  color: #fdd835;
}

p.JavaScript i {
  color: #fdd835;
}

p.Python i {
  color: #3572A5;
}

p.Vue i {
  color: #2C3E50;
}

p.CSS i {
  color: #563D7C;
}

p.SASS i {
  color: #e91e63;
}

p.HTML i {
  color: #E34C26;
}

p.TypeScript i {
  color: #0d47a1;
}

p.Ruby i {
  color: #b71c1c;
}

p.PHP i{
  color: "#4F5D95";
}

</style>
