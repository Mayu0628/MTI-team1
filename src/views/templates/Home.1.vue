<template>
  <div>
    <!--<Loading :isShow='isLoading' />-->
    <div class="ui main container">
      <!-- 基本的なコンテンツはここに記載する -->
      <!--<Message :isShow='message.isShow' :isError='message.isError' :message='message.text'/>-->
      <div class="ui segment">
        <form class="ui form" @submit.prevent="getSearchedArticles">
          <div class="field">
            <label for="nickname">ユーザーID</label>
            <input v-model="search.userId" type="text" id="userId" name="userId" placeholder="ユーザーID" />
          </div>

          <div class="field">
            <label>カテゴリー名</label>
            <input v-model.number="search.category" type="text" id="category" name="category" placeholder="カテゴリ"/>
          </div>
    
          <div class="field">
            <label>投稿日時</label>
            <div class="inline fields">
              <div class="field">
                <input v-model.number="search.start" type="date" name="datestart">
                <label for="datestart">から</label>
              </div>
              <div class="field">
                <input v-model.number="search.end" type="date" name="dateend">
                <label for="dateend">まで</label>
              </div>
            </div>
          </div>
          <button class="ui button huge green fluid" type="submit">
            検索
          </button>
        </form>
      </div>
      
    </div>
  </div>
</template>

<script>
// 必要なものはここでインポートする
// @は/srcの同じ意味です
// import something from '@/components/something.vue';
import { baseUrl } from '@/assets/config.js'
// import Message from '@/components/Message.vue'
// import Loading from '@/components/Loading.vue'

export default {
  name: 'Search',
  components: {
    // 読み込んだコンポーネント名をここに記述する
    // Message,
    // Loading
  },
  data() {
    // Vue.jsで使う変数はここに記述する
    return {
      search: {
        userId: null,
        category: null,
        start: null,
        end: null,
      },
    };
  },

  computed: {
    // 計算した結果を変数として利用したいときはここに記述する
    filteredUsers() {
      let result = this.users;
      if (this.nickname) {
        result = result.filter(target => {
          return target.nickname.match(this.nickname)
        })
      }
      
      if (this.start) {
        result = result.filter(target => {
          return target.age >= this.start
        })
      }
      
      if (this.end) {
        result = result.filter(target => {
          return target.age <= this.end
        })
      }
      
      return result
    },
    
    filteredUsers() {
      return this.users.filter(e => 
        e.nickname?.match(this.nickname)
        && e.age >= this.start
        && e.age <= this.end
      )
    }
  },
  
  created: async function() {
    this.isLoading = true
    try {
      const headers = {'Authorization': window.localStorage.getItem('token')}
      /* global fetch */
      const res = await fetch(baseUrl + '/users', {
        method: 'GET',
        headers
      })
      
      const text = await res.text()
      const jsonData = text ? JSON.parse(text) : {}
      
      if (!res.ok) {
        throw new Error(jsonData.message ?? 'エラーメッセージがありません')
      }
      
      this.users = jsonData
    } catch(e) {
      this.message.isShow = true
      this.message.text = e.message ?? 'エラーメッセージがありません';
    }
    this.isLoading = false
    return
  },
}
</script>
