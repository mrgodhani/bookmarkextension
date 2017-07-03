<template>
  <div id="app" class="popup-content">
    <div v-if="token === null">
      <md-input-container>
        <label>Add Firebase App Id</label>
        <md-input v-model="firebaseId"></md-input>
      </md-input-container>
      <md-button class="md-primary" style="float:right" @click="addAppId(firebaseId)">Add</md-button>
    </div>
    <div v-if="token !== null">
      <p>
        <img :src="bookmark.favicon" width="15" height="15">
      </p>
      <p>
        <span class="md-title"> {{ bookmark.title }}</span>
      </p>
      <p>
        <span class="md-body-1">{{ bookmark.description }}</span>
      </p>
      <md-input-container>
        <label for="category">Choose Category</label>
        <md-select name="category" id="category" v-model="category">
          <md-option :value="category.name" v-for="category in categories" :key="category.name">{{ category.name }}</md-option>
        </md-select>
      </md-input-container>
      <md-button class="md-primary" style="float:right" @click="addBookmark(bookmark)">Add Bookmark</md-button>
    </div>
  </div>
</template>
<script>
import ext from "./utils/ext";
import storage from "./utils/storage";
import firebase from 'firebase';

export default {
  data() {
    return {
      firebaseId: null,
      token: null,
      category: null,
      categories: [],
      bookmark: {
        title: null,
        description: null,
        url: null,
        favicon: null
      }
    }
  },
  created() {
    const self = this;
    storage.get('id', function (res) {
      if (res.id) {
        self.token = res.id;
        const databaseUrl = `${self.token}.firebaseio.com`;
        const fire = firebase.initializeApp({
          databaseURL: databaseUrl
        });
        const db = fire.database();
        const categories = db.ref('categories');
        const bookmarks = db.ref('bookmarks');
        categories.on('value', (snapshot) => {
          self.categories = snapshot.val();
        }, function (err) {
          console.log(err.code);
        });
      }
    })

    ext.tabs.query({ active: true, currentWindow: true }, function (tabs) {
      var activeTab = tabs[0];
      self.bookmark.title = activeTab.title;
      self.bookmark.url = activeTab.url;
      self.bookmark.favicon = activeTab.favIconUrl
    });
  },
  methods: {
    renderBookmark(data) {
      console.log(data)
      // this.bookmark.title = data.title;
      // this.bookmark.description = data.description;
      // this.bookmark.url = data.url;
    },
    addBookmark(data) {

    },
    addAppId(id) {
      storage.set({ 'id': id }, function () {
        console.log('Firebase Id used');
      });
      this.token = id
    }
  }
}
</script>